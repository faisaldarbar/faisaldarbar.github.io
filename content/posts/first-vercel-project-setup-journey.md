---
title: "First Vercel Project Setup Journey"
date: 2025-07-25T02:30:00Z
description: "Documenting the first attempt to deploy a SaaS starter template on Vercel using Next.js — ultimately abandoned due to Stripe limitations in India."
categories: ["Dev Docs"]
tags: [vercel, nextjs, saas, deployment, stripe, neon, supabase, github, environment-variables, troubleshooting, india, project-setup]

cover:
  image: "/images/first-vercel-project-setup-journey.webp"
  alt: "First Vercel Project Setup Journey"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

### ✅ Step 1: **Signed Up on Vercel**

* Visited vercel.com
* Signed up using GitHub
* Granted Vercel permission to access all repositories (default option)
* Vercel automatically fetched all repos under the GitHub account

---

### ✅ Step 2: **Selected a Template**

* On Vercel’s dashboard, chose the **“Next.js SaaS Starter”** template.
* It auto-filled the project name: `next-js-saas-starter`
* Clicked **"Create"** to proceed with the setup

---

### ✅ Step 3: **Chose a Database**

* Vercel prompted to add a data store (PostgreSQL):

  * Chose: **Neon** (serverless Postgres)
* Database name auto-filled as `neon-emerald-chair`
* Region: `Washington, D.C. (iad1)`
* Chose to integrate the database with all environments (Development, Preview, Production)
* Enabled option: "Neon: Create database branch for deployment"
* Clicked **Create Database**

---

### ✅ Step 4: **Environment Variables Prompt**

Vercel then asked for environment variables:

| Variable Name           | Description                          | Status               |
| ----------------------- | ------------------------------------ | -------------------- |
| `AUTH_SECRET`           | Random string for session encryption | Auto-filled          |
| `STRIPE_SECRET_KEY`     | Secret key from Stripe               | ❌ Not Available      |
| `STRIPE_WEBHOOK_SECRET` | Webhook secret from Stripe dashboard | ❌ Not Available      |
| `BASE_URL`              | The production URL of your app       | ❌ Not yet configured |

* Clicked on "Learn More", which redirected to the GitHub repo with setup instructions.

---

### ✅ Step 5: **Cloned the Repo Locally**

* Cloned the template repo locally in VS Code using:

  ```bash
  ctrl + shift + p > Git: Clone
  ```
  
* Opened the file: `app/(dashboard)/pricing/page.tsx`
* Confirmed the Stripe integration was active — it pulls prices and product info via:

  ```ts
  getStripePrices(), getStripeProducts()
  ```

---

### ✅ Step 6: **Committed & Pushed Code to GitHub**

* Made sure no changes were needed yet
* Committed and pushed the project back to GitHub so Vercel could auto-deploy

---

### ✅ Step 7: **Deploy Attempt on Vercel**

* Clicked **Deploy**
* Build process began, installed dependencies, compiled successfully
* BUT ❌ **Deployment failed during page prerendering** due to missing Stripe credentials

---

### ❌ Final Error Summary

```
Error occurred prerendering page "/pricing"
→ Error: Invalid API Key provided: sk_test_******7890
→ StripeAuthenticationError: 401 Unauthorized
```

### ⚠️ Cause:

* Stripe is **not available in India**
* No valid Stripe account or API key
* Pricing page relies directly on Stripe API calls

---

## 🧹 Decision: **Abandon This Project & Start From Scratch**

Given the limitations:

* Stripe is not usable in your region
* The template is tightly coupled with Stripe
* You want to **learn by building step-by-step**

➡️ **We are deleting this Vercel project and returning to the drawing board.**

---

## 🧹 Clean-Up: Tearing Down the Abandoned Setup

Before we move forward with building our own SaaS from scratch, here’s how I cleaned up everything from the initial attempt:

### 🔻 Vercel: Delete the Project

* Go to Vercel Dashboard
* Select the project (`next-js-saas-starter`)
* Click **Settings** → **General**
* Scroll down and click **Delete Project**

### 🔗 GitHub: Repository Removal

* I had granted Vercel access to all my GitHub repositories — (Faisal Darbar from the future, NetworkChuck style) Removed that..
* Deleted the repo directly from GitHub by visiting the repo page and navigating to:
  **Settings** → **Danger Zone** → **Delete this repository**

### 🧼 Local Dev Environment

* Deleted the cloned project folder from my local development workspace (in WSL)

### 🗃️ Neon: Remove Linked Database & Uninstall Integration

To completely clean up the Neon Postgres integration used during this attempt:

1. Go to your project’s **Dashboard** on Vercel
2. Navigate to the **Integrations** tab
3. Click **Manage** next to **Neon (Storage) (Billed via Vercel)**
4. Under “Linked Products,” delete any listed (e.g. `next-js-saas-starter`)
5. Click on your Neon database project (e.g. `neon-emerald-chair`)
6. Go to **Settings** and click **Delete Database**
7. Go back to **Integrations** → **Neon** → **Manage**
8. Navigate to **Settings** and click **Uninstall Integration**

This ensures that both your database and the Vercel-Neon integration are fully removed.

---

## 🎯 Next Step: Build Our Own SaaS from Scratch

We'll now:

1. **Design our own architecture** — minimal, Stripe-free to start
2. Use **Next.js (app router)**, **Tailwind CSS**, and a **PostgreSQL DB** (via Neon or Supabase)
3. Add payments later (Razorpay for India, for example)

