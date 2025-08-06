---
title: "📌 Personal Brand Site Architecture Blueprint"
weight: 3
date: 2025-07-30T02:30:00Z
description: "This document outlines the finalized and future-proof architecture for Faisal Darbar's personal brand ecosystem, comprising modular services across subdomains, using a self-hosted and cost-effective stack."
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
**Stack:** Hugo + Giscus + GitHub Pages

- Static content: blog, about, contact
- SEO-optimized and fast
- Newsletter via ConvertKit
- Comments via Giscus
- Hosted on GitHub Pages

✅ Done and live

---

## 🛍️ Storefront – `store.faisaldarbar.com`
**Stack:** React + TailwindCSS + Vite

- Static SPA for digital/physical products
- Product catalog and detail pages
- Razorpay Buy Now integration (guest checkout)
- Inventory indicators (in stock, low stock, out of stock)
- Tokenized download links for digital products
- Hosted via GitHub Pages or Netlify (free tier with caution)

---

## 🔧 Backend API – `api.faisaldarbar.com`
**Stack:** Node.js + Express + Prisma + PostgreSQL (self-hosted)

- Razorpay webhook validation
- Order logging and processing
- Generates secure download links (JWT/UUID with expiry)
- Sends transactional emails via Resend
- Stores product and order data in PostgreSQL
- Rate limiting and secure headers with Helmet.js

---

## 🔐 Admin Dashboard – `admin.faisaldarbar.com`
**Stack:** React + TailwindCSS

- Secure login with password + 2FA (TOTP via otplib)
- Manage products (add/edit/hide)
- View and fulfill orders
- Upload digital files
- View dashboard widgets:
  - Sales graphs
  - Newsletter stats (via ConvertKit API)
  - Download counts

---

## ✉️ Integrations & Services
| Service      | Purpose                                           |
|--------------|---------------------------------------------------|
| Razorpay     | Payment checkout and webhooks                     |
| Resend       | Transactional emails (order delivery, receipts)   |
| ConvertKit   | Newsletter and subscriber analytics               |
| Giscus       | Blog comments on Hugo site                        |
| GitHub Pages | Static deployments for Hugo and Storefront        |
| Cloudflare   | Proxying, DNS, custom error pages, and tunnels    |

---

## 🧱 Stack Summary
| Layer           | Stack                            |
|----------------|----------------------------------|
| Static Site     | Hugo                            |
| Storefront App  | React + TailwindCSS             |
| Admin Dashboard | React + TailwindCSS             |
| Backend API     | Node.js + Express + Prisma      |
| Database        | PostgreSQL (self-hosted)        |
| Payments        | Razorpay                        |
| Emails          | Resend                          |
| Newsletter      | ConvertKit                      |
| Hosting         | GitHub Pages + Proxmox          |

---

## 🛡️ Security & DevOps
- `.env` + dotenv for managing secrets
- HTTPS via Cloudflare Tunnel + Let’s Encrypt
- Secure headers with helmet.js
- Logging with Winston or Pino
- GitHub Actions for CI/CD pipelines
- Docker for backend deployment on Proxmox
- Custom Cloudflare error pages for power outages

---

## 🚧 Background Tasks (Future Enhancements)
- Basic webhook retry mechanism (manual or CRON)
- BullMQ + Redis (optional) for scalable job queues
- Email delivery retries

---

## 🧠 Planned Expansion Ideas
- Dashboard widgets: sales, downloads, subscriber growth
- Analytics dashboard with charts and insights
- Light CRM layer using ConvertKit API
- Affiliate system for digital products

---

## 🗺️ Build Priority Roadmap
**Phase 1 - MVP:**
- Hugo site live (✅)
- Storefront + API for order/digital delivery
- Admin panel (basic) + 2FA

**Phase 2 - Enhanced Functionality:**
- Inventory visibility
- Dashboard widgets
- Background job retry logic

**Phase 3 - Scaling:**
- Redis/BullMQ integration
- Additional roles & permissions
- CRM, analytics, affiliate system
- Multi-user admin dashboard (optional)
- Postgres backups + monitoring tools
- Optional: Self-hosted analytics (Plausible/Umami)
- Optional: File CDN for digital downloads (Cloudflare R2 or Bunny.net)

