---
title: "Next.js Site Master Plan"
date: 2025-07-29T02:30:00Z
description: "A phased, feature-complete roadmap for building a personal brand site from scratch using Next.js App Router, Supabase, MDX, and other modern tools — including blogging, paid content, digital product sales, and private journaling."
categories: ["Dev Docs"]
tags: [nextjs, supabase, mdx]
---

## 🧩 Core Features

### ✅ Blog System
- MDX-based blog posts (public + gated)
- Frontmatter metadata (title, slug, visibility)
- Visibility levels: `public`, `paid`, `private` (only for me)
- Draft support: `draft: true`
- Content preview without triggering Netlify rebuilds
- Decap CMS for editing (no dashboard yet)

### ✅ Storefront
- Razorpay integration (digital + physical)
- Cart system (client-side, Zustand or React Context)
- Product types: digital (auto delivery) + merch
- Webhooks from Razorpay → Supabase update → transactional email
- Admin role-based store controls (add/edit/hide products)

### ✅ User System
- Supabase Auth (email/password)
- Email verification enabled
- `users` table with `role` or `tier` (`free`, `paid`, `admin`)
- Auth-guarded routes (paid content, dashboard, downloads)
- Profile page + account settings
- Role-based content access

### ✅ Content Features
- Private content (my diary) using role/email check
- Public posts + paid gated posts
- RSS feed
- Search (TBD)
- Giscus for comments
- ConvertKit newsletter

### ✅ UI & UX
- TailwindCSS + shadcn/ui
- Responsive layout
- Dark/light mode
- Skeleton loaders / toasts / states
- Custom 404 and error pages

### ✅ SEO & Accessibility
- Per-page `<title>`, `<meta>` tags
- Sitemap.xml, robots.txt
- OG images
- Lighthouse best practices
- Aria roles + keyboard nav (a11y)

### ✅ Infrastructure
- Hosting: Netlify
- DB + Auth: Supabase
- Emails: Resend
- Webhooks: Razorpay, future others
- Logging/Monitoring: TBD (e.g. Sentry or LogSnag)
- Image optimization: WebP, responsive sizes

---

## 🔁 Phase Plan

### 🔹 Phase 1: MVP
- Static blog with MDX
- Public blog index and post pages
- Decap CMS editing
- Drafts + visibility via frontmatter
- Sitemap + SEO basics

### 🔹 Phase 2: Auth + Private Content
- Supabase auth (email/password)
- Role-based post access
- Private route for diary (my eyes only)
- Auth-guarded pages + preview system

### 🔹 Phase 3: Paid Content + Gating
- Add `paid` visibility level
- Payment tier detection (Supabase field)
- Guarded content, download gating
- ConvertKit newsletter signup

### 🔹 Phase 4: Digital Store
- Razorpay checkout
- Cart system (React state)
- Product delivery emails (Resend)
- Razorpay webhook + Supabase order record
- Admin dashboard (basic)

### 🔹 Phase 5: Physical Store + Polishing
- Razorpay shipping form / address info
- Product inventory tracking
- UI polish + animations
- Analytics + a11y pass
- Blog post previews from CMS (no rebuild)

---

## 📎 Developer Notes Appendix

### 🔒 Security & Stability
- Rate limiting for login, webhook, form APIs
- Zod for input validation
- RLS (Row Level Security) in Supabase to protect private/paid data
- Email verification required (Supabase setting)
- Never expose `service_role` or secret keys client-side

### ⚙️ Project Structure Tips
- Feature-based folder layout
- Use `@/` for absolute imports
- Separate `components`, `lib`, `types`, `utils`, `hooks`
- Use consistent naming (`kebab-case` for files, `PascalCase` for components)

### 🌱 Development Tips
- Use `.env.example` to track env vars
- Commit checkpoints before big changes
- Use dummy data with `faker` early on
- Decouple frontend logic from Supabase-specific functions

### 📤 Deployment & DevOps
- Netlify build: `npm run build && npm run export`
- Use Netlify redirects in `_redirects`
- Monitor Supabase limits manually
- Add RSS + sitemap plugins

### 🧪 Testing & QA
- Manual testing by phase is fine for solo
- Consider `playwright` or `cypress` for login/checkout later
- Mock webhooks & email previews during dev

### 📚 Learning & Docs
- Bookmark official docs:
  - [Next.js App Router](https://nextjs.org/docs/app)
  - [Supabase Docs](https://supabase.com/docs)
  - [Tailwind UI](https://tailwindui.com/components)
  - [Resend Docs](https://resend.com/docs)
- Document tricky flows in `/docs`
- Keep a solid `README.md`
