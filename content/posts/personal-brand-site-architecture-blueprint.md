---
title: "Personal Brand Site Architecture Blueprint"
date: 2025-07-30T02:30:00Z
description: "This is a working blueprint of my planned website architecture — a modular setup using Hugo, React, and Express across subdomains."
categories: ["Dev Docs"]
tags: [hugo, react, express]

cover:
  image: "/images/personal-brand-site-architecture-blueprint.webp"
  alt: "Personal Brand Site Architecture Blueprint"
  caption: "AI Generated Photo by ChatGPT"
---


This post is meant to be my development guide.

---

## 🌐 Main Site – `faisaldarbar.com`
- Built with **Hugo**
- Static site: blog, about, contact, branding
- SEO-optimized and fast
- Integrated with **ConvertKit** for newsletter
- Hosted on **GitHub Pages**

---

## 🛍️ Storefront – `store.faisaldarbar.com`
- Built with **React + TailwindCSS**
- Product catalog for digital and physical goods
- Product detail pages
- "Buy Now" integration with **Razorpay**
- Guest checkout (no user auth)
- Hosted as a static app

---

## 🔧 Backend API – `api.faisaldarbar.com`
- Built with **Node.js + Express**
- Handles:
  - Razorpay webhook validation
  - Order logging and processing
  - Sending transactional emails via **Resend**
- Lightweight database (SQLite or JSON)
- Self-hosted on **Proxmox** using **Cloudflare Tunnel**

---

## 🔐 Admin Dashboard – `admin.faisaldarbar.com`
- Internal tool for managing store
- Built with **React**
- Basic auth (JWT or password-based)
- Allows:
  - Adding/editing/hiding products
  - Viewing order logs
  - Uploading digital files
  - Marking fulfillment status

---

## ✉️ Integrations & Services
- **Razorpay**: Payment checkout and webhooks
- **Resend**: Transactional emails (order delivery, receipts)
- **ConvertKit**: Newsletter (on main Hugo site)

---

## 🧱 Stack Summary

| Layer         | Stack                       |
|---------------|-----------------------------|
| Static Site   | Hugo                        |
| Frontend App  | React + TailwindCSS         |
| Backend API   | Express on Node.js          |
| Hosting       | Proxmox + Cloudflare Tunnel |
| Payments      | Razorpay                    |
| Emails        | Resend                      |
| Newsletter    | ConvertKit                  |

---

This document serves as a central reference for ongoing work. Any features not listed here are either optional or deferred.

