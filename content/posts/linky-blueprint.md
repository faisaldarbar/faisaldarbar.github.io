---
title: "📌 Linky Blueprint"
weight: 4
date: 2025-07-30T02:40:00Z
description: "A self-hosted URL shortener and analytics tool built with Next.js — designed for creators, marketers, and indie founders."
categories: ["Dev Docs"]
tags: [nextjs, url-shortener, self-hosted, indiehackers, saas-ideas, open-source, razorpay, web-development, side-project, startup, postgres, prisma, tailwindcss, fullstack-javascript]

cover:
  image: "/images/linky-blueprint.webp"
  alt: "Linky Blueprint"
  caption: "AI Generated Photo by ChatGPT"
---

Deploying at: `linky.faisaldarbar.com`

---

## 🔹 Project Goals
- Build a fully working, self-hosted link shortener
- Track click data: timestamps, referrer, location (IP-based)
- Clean and privacy-conscious (no creepy tracking)
- Add optional monetization via Razorpay (phase 2)
- Learn and apply Next.js, API routes, database, SSR

---

## 🧱 Stack
| Layer       | Tech                                    |
|-------------|------------------------------------------|
| Frontend    | Next.js (App Router), Tailwind CSS       |
| Backend     | Next.js API Routes, Node.js, Razorpay SDK (optional) |
| Database    | SQLite for dev → Postgres (self-hosted)  |
| Auth (admin)| Clerk or magic links / basic auth        |
| Hosting     | Self-hosted on Proxmox/Hetzner/VPS       |
| Domain      | `linky.faisaldarbar.com`                 |

---

## 🗂 App Structure
```
/linky
├── pages (or app/ if using App Router)
│   ├── index.tsx (create + view links)
│   ├── [shortcode].tsx (redirect handler)
├── /api
│   ├── create.ts (generate short link)
│   ├── track.ts (log analytics)
│   └── verify-payment.ts (Razorpay, optional)
├── lib/
│   ├── db.ts (Prisma or raw DB queries)
│   └── utils.ts (random slug, etc.)
├── prisma/
│   └── schema.prisma
├── .env
├── next.config.js
```

---

## 🎯 Core Features (MVP)
- 🔗 Shorten long URLs to custom slugs
- 📊 Basic analytics (total clicks, referrer, timestamp)
- 📍 Optional geolocation via IP (no cookies)
- ⏰ Set expiration or limit on number of uses (optional)
- 🧑‍💻 Admin-only dashboard to view/manage links
- 🔒 Auth (magic link or simple passcode for MVP)

---

## 🚀 Phase 2 – Monetized Links (Optional)
- 💸 Razorpay integration: create link → pay → unlock redirect
- 🔐 API verifies payment → returns actual destination
- 🧾 Email or page shows receipt/confirmation

---

## 📈 Phase 3 – Advanced Analytics
- View individual link performance: CTR, traffic sources
- Dashboard charts (Recharts, Chart.js)
- Export CSV

---

## 🛡️ Security & Deployment
- Use Helmet.js for backend security headers
- Rate-limiting on API routes
- Logging with Pino or Winston
- Environment variables via dotenv
- GitHub Actions for CI/CD

---

## 💡 Future Ideas
- Team/multi-user support
- Custom domain per user
- Link folders / categories
- Tag links by campaign (UTM builder)
- Razorpay payout dashboard (for creators earning through links)
- Invite system for early access

---

## 📍 Initial Hosting Plan
- Self-hosted on your Ubuntu server via Proxmox
- Reverse proxy via Nginx + SSL
- Subdomain: `linky.faisaldarbar.com`
- SQLite for now → PostgreSQL later (local)

---

## ✅ Next Steps
- [ ] Initialize Next.js app with TailwindCSS
- [ ] Set up DB schema using Prisma (with SQLite first)
- [ ] Build URL shortening form (slug input + long URL input)
- [ ] Create API route to store short URL and long URL
- [ ] Build redirect route handler (e.g., `/[slug]` → redirect)
- [ ] Add click tracking (store timestamp, referrer, IP)
- [ ] Create simple admin dashboard (protected route)
- [ ] Add basic auth (env password or magic link)
- [ ] Deploy to VPS and point subdomain
- [ ] Monitor logs + errors

---

Let’s build it!

