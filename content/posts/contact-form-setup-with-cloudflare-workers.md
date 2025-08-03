---
title: "Contact Form Setup with Cloudflare Workers"
date: 2025-07-24T02:30:00Z
description: "This guide walks you through building and deploying a secure, spam-protected contact form."
categories: ["Dev Docs"]
tags: [cloudflare-workers, recaptcha-v3, brevo-api, contact-form, serverless]

cover:
  image: "/images/contact-form-setup-with-cloudflare-workers.webp"
  alt: "Contact Form Setup with Cloudflare Workers"
  caption: "Photo by Miles Burke on Unsplash"

---

A contact form with:

* ✅ Cloudflare Workers (as serverless backend)
* ✅ Google reCAPTCHA v3 (invisible, automated bot detection)
* ✅ Brevo (Sendinblue) for transactional email
* ✅ Honeypot field (for extra spam protection)
* ✅ Secrets management via Wrangler CLI
* ✅ Works with any frontend (e.g., Hugo, React, plain HTML)

---

## 🧰 1. Install Wrangler CLI

```bash
npm install -g wrangler
wrangler --version  # Confirm it's installed
```

---

## 🏗️ 2. Scaffold the Worker

```bash
mkdir contact-worker && cd contact-worker
wrangler init --no-git --type javascript
```

✅ This creates:

* `wrangler.toml` or `wrangler.jsonc`
* `src/index.js`

---

## 🔐 3. Add Secrets to Cloudflare

You'll securely store API keys using Wrangler. They will **not** be visible in your code.

```bash
npx wrangler secret put BREVO_API_KEY
npx wrangler secret put RECAPTCHA_SECRET_KEY
```

Paste the respective values when prompted:

* `BREVO_API_KEY` from Brevo SMTP & API settings
* `RECAPTCHA_SECRET_KEY` from your Google reCAPTCHA v3 project

---

## ✍️ 4. Replace `src/index.js` with Worker Logic

```js
function corsHeaders() {
  return {
    "Access-Control-Allow-Origin": "https://aptxlabs.com", // Or "*" if testing locally
    "Access-Control-Allow-Methods": "POST, OPTIONS",
    "Access-Control-Allow-Headers": "Content-Type",
  };
}

export default {
  async fetch(request, env, ctx) {
    if (request.method === "OPTIONS") {
      // Handle CORS preflight
      return new Response(null, {
        status: 204,
        headers: corsHeaders(),
      });
    }

    if (request.method !== "POST") {
      return new Response("Method Not Allowed", {
        status: 405,
        headers: corsHeaders(),
      });
    }

    try {
      const formData = await request.formData();
      const email = formData.get("email");
      const name = formData.get("name");
      const message = formData.get("message");
      const botField = formData.get("bot-field");
      const recaptchaToken = formData.get("g-recaptcha-response");

      if (botField) {
        return new Response("Spam detected", {
          status: 400,
          headers: corsHeaders(),
        });
      }

      const recaptchaVerify = await fetch("https://www.google.com/recaptcha/api/siteverify", {
        method: "POST",
        headers: { "Content-Type": "application/x-www-form-urlencoded" },
        body: `secret=${env.RECAPTCHA_SECRET_KEY}&response=${recaptchaToken}`,
      });

      const recaptchaResult = await recaptchaVerify.json();
      if (!recaptchaResult.success || (recaptchaResult.score !== undefined && recaptchaResult.score < 0.5)) {
        return new Response("reCAPTCHA failed", {
          status: 403,
          headers: corsHeaders(),
        });
      }

      const emailData = {
        sender: { name: name || "Website Contact", email: "no-reply@aptxlabs.com" },
        to: [{ email: "fx@aptxlabs.com", name: "APT XLabs" }],
        replyTo: { email },
        subject: `New message from ${name}`,
        textContent: `Name: ${name}\nEmail: ${email}\n\n${message}`
      };

      const emailResp = await fetch("https://api.brevo.com/v3/smtp/email", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "api-key": env.BREVO_API_KEY
        },
        body: JSON.stringify(emailData),
      });

      if (emailResp.ok) {
        return new Response("Message sent!", {
          status: 200,
          headers: corsHeaders(),
        });
      } else {
        const errorText = await emailResp.text();
        return new Response("Email send failed: " + errorText, {
          status: 500,
          headers: corsHeaders(),
        });
      }
    } catch (err) {
      return new Response("Server error: " + err.message, {
        status: 500,
        headers: corsHeaders(),
      });
    }
  }
};
```

---

## 🚀 5. Deploy to Cloudflare

This command will deploy your backend worker to Cloudflare.

```bash
npx wrangler deploy
```

✅ You'll get a public URL like:

```
https://contact-worker.YOURNAME.workers.dev
```

This is where your HTML form will POST submissions.

> You need to insert the above URL into the action field of your form as below.

---

## 📄 6. HTML Contact Form

Here’s the frontend form you can include in your Hugo/HTML site:

```html
<form
  id="contact-form"
  action="https://contact-worker.YOURNAME.workers.dev"
  method="POST"
  class="contact-form"
  style="max-width: 600px; margin: 0 auto;"
>
  <input type="hidden" name="bot-field" />

  <div style="margin-bottom: 1rem;">
    <label for="name" style="display: block; font-weight: bold; margin-bottom: 0.5rem;">Name</label>
    <input
      type="text"
      id="name"
      name="name"
      required
      placeholder="Your name"
      style="width: 100%; padding: 0.5rem; border: 1px solid #ccc; border-radius: 4px;"
    />
  </div>

  <div style="margin-bottom: 1rem;">
    <label for="email" style="display: block; font-weight: bold; margin-bottom: 0.5rem;">Email</label>
    <input
      type="email"
      id="email"
      name="email"
      required
      placeholder="Your email"
      style="width: 100%; padding: 0.5rem; border: 1px solid #ccc; border-radius: 4px;"
    />
  </div>

  <div style="margin-bottom: 1rem;">
    <label for="message" style="display: block; font-weight: bold; margin-bottom: 0.5rem;">Message</label>
    <textarea
      id="message"
      name="message"
      rows="5"
      required
      placeholder="Your message"
      style="width: 100%; padding: 0.5rem; border: 1px solid #ccc; border-radius: 4px;"
    ></textarea>
  </div>

  <button
    type="submit"
    style="background-color: #007bff; color: white; padding: 0.75rem 1.5rem; border: none; border-radius: 4px; cursor: pointer;"
  >
    Send Message
  </button>

  <div id="form-status" style="margin-top: 1rem; font-weight: bold;"></div>
</form>

<!-- ✅ reCAPTCHA v3 Script: replace with your actual site key -->
<script src="https://www.google.com/recaptcha/api.js?render=YOUR_SITE_KEY"></script>

<!-- ✅ Form Handling Script -->
<script>
  window.addEventListener("load", function () {
    const form = document.getElementById("contact-form");
    const status = document.getElementById("form-status");

    form.addEventListener("submit", function (e) {
      e.preventDefault();
      status.textContent = "Sending...";
      status.style.color = "#555";

      if (typeof grecaptcha === "undefined") {
        status.textContent = "reCAPTCHA not loaded. Please try again.";
        status.style.color = "#dc3545";
        return;
      }

      grecaptcha.ready(function () {
        grecaptcha.execute("YOUR_SITE_KEY", { action: "contact" }).then(function (token) {
          const formData = new FormData(form);
          formData.append("g-recaptcha-response", token);

          fetch(form.action, {
            method: "POST",
            body: formData,
          })
            .then(async (response) => {
              const text = await response.text();
              if (response.ok) {
                status.textContent = "✅ Message sent successfully!";
                status.style.color = "#28a745";
                form.reset();
              } else {
                status.textContent = "❌ Failed: " + text;
                status.style.color = "#dc3545";
              }
            })
            .catch((err) => {
              console.error("Submission error:", err);
              status.textContent = "❌ Error: " + err.message;
              status.style.color = "#dc3545";
            });
        });
      });
    });
  });
</script>
```

🟢 Insert YOUR_SITE_KEY in the **TWO** places in the above html.

---

## 🔁 Updating Secrets Later

You can re-run these any time:

```bash
npx wrangler secret put BREVO_API_KEY
npx wrangler secret put RECAPTCHA_SECRET_KEY
npx wrangler deploy
```

---

## ☁️ Where Are My Secrets Stored?

All secrets are stored securely in **Cloudflare's environment**.

---

## ✅ Final Checklist

* [x] Worker deployed and responding to POST
* [x] Form submits and calls the Worker
* [x] reCAPTCHA v3 badge visible and functioning
* [x] Email arrives in Brevo inbox
