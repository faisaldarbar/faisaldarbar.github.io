---
layout: page
icon: fas fa-envelope
order: 6
---


<p>Send me a message:</p>

<form id="my-form" action="https://formspree.io/f/mblyqqok" method="POST">
  <label for="email"><strong>Email</strong></label><br>
  <input type="email" name="email" id="email" required><br><br>

  <label for="message"><strong>Message</strong></label><br>
  <textarea name="message" id="message" rows="5" required></textarea><br><br>

  <!-- ✅ Google reCAPTCHA -->
  <div class="g-recaptcha" data-sitekey="6LdUop0gAAAAANw7Q5G2wffaxTxMmWF-TCd_BwHk"></div><br>

  <button id="my-form-button" type="submit">Submit</button><br><br>
  <p id="my-form-status"></p>
</form>

<!-- ✅ Load reCAPTCHA script -->
<script src="https://www.google.com/recaptcha/api.js" async defer></script>

<script>
  var form = document.getElementById("my-form");

  async function handleSubmit(event) {
    event.preventDefault();
    var status = document.getElementById("my-form-status");

    // ✅ Check if reCAPTCHA passed
    var recaptchaToken = grecaptcha.getResponse();
    if (!recaptchaToken) {
      status.innerHTML = "<span style='color:red;'>Please verify that you're not a robot.</span>";
      return;
    }

    // ✅ Append reCAPTCHA token to form data
    var data = new FormData(event.target);
    data.append("g-recaptcha-response", recaptchaToken);

    fetch(event.target.action, {
      method: form.method,
      body: data,
      headers: {
        'Accept': 'application/json'
      }
    }).then(response => {
      if (response.ok) {
        status.innerHTML = "<span style='color:green;'>Thanks for your submission!</span>";
        grecaptcha.reset(); // reset reCAPTCHA
        form.reset();
      } else {
        response.json().then(data => {
          if (Object.hasOwn(data, 'errors')) {
            status.innerHTML = "<span style='color:red;'>" + data["errors"].map(error => error["message"]).join(", ") + "</span>";
          } else {
            status.innerHTML = "<span style='color:red;'>Oops! There was a problem submitting your form.</span>";
          }
        });
      }
    }).catch(error => {
      status.innerHTML = "<span style='color:red;'>Oops! There was a problem submitting your form.</span>";
    });
  }

  form.addEventListener("submit", handleSubmit);
</script>