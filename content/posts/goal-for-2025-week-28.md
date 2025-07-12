---
title: "Goal for 2025 Week 28 – Replicate Design to New Hugo Theme & Migrate Site Content"
date: 2025-07-12T12:00:00Z
draft: false
---

## 🧭 What's the Plan?

This week, the mission is to **cleanly separate the theme (design)** from the **site content**, and bring both under proper version control so the site is maintainable, scalable, and dev-friendly moving forward.

---

## ✅ Current Setup Recap

- I've forked the [PaperMod theme](https://github.com/adityatelange/hugo-PaperMod) and started working on my **own version**: `hugo-papermod-fd`
- I created a **demo/testing site** using: `hugo new site hugo-papermod-fd-demo --format yaml`
- The forked theme is added as a **git submodule** to the demo using:

  ```bash
  git submodule add ../hugo-papermod-fd themes/hugo-papermod-fd
  ```

- The theme loads and works locally on `localhost` via `hugo server`.

---

## 🎯 Goal for This Week

> Get my live site (faisaldarbar.com) running cleanly on my forked theme (`hugo-papermod-fd`), using the new demo structure.

---

## ✍️ Tasks Breakdown

### 🔁 1. Copy Over Content from Old Repo

Copy only the **content files** (like `content/`, `config/`, `static/`, `assets/`, etc.) from `faisaldarbar.github.io` into `hugo-papermod-fd-demo`.

❗️**Do NOT copy any theme/layout files** — this is the job of the submodule now.

---

### 🎨 2. Migrate Theme Customizations to `hugo-papermod-fd`

All design/layout changes I’ve made until now (which were previously done inside the site repo) must now go inside the theme submodule repo (`hugo-papermod-fd`).

#### ✅ Example:

Previously changed inside the site’s `layouts/_default/baseof.html`:

```html
<head>
  {{- partial "head.html" . }}
</head>
```

➡️ Now changed to:

```html
<head>
  {{- partial "head.html" . }}
  {{- partial "head-extra.html" . }}
</head>
```

✅ This change should now live in:  
`hugo-papermod-fd/layouts/_default/baseof.html`  
and be **committed to the theme repo only**.

---

### 🚀 3. Prepare for Live Launch

Once both content and theme changes are done and working locally:

- Rename `hugo-papermod-fd-demo` ➝ `faisaldarbar.github.io`
- Remove the current `faisaldarbar.github.io` (after backup)
- Push my custom theme to my Hugo PaperMod theme forked repository, and my updated site with the latest content and design to: `https://faisaldarbar.com`.

---

## 💡 Why This Matters

Normally, when we use a theme as a submodule, we copy over theme files into the project and make changes there.

But in this case, I’m building a theme for myself, for the first time ever — so I’m officially a dev now 😎

I plan to share the theme on GitHub for others to use. That’s why I'm **keeping the theme and the content separate**, using the submodule approach....

---

## 🛠️ Bonus Tip

Make sure to:

```bash
git add .
git commit -m "Theme + content separation complete"
git push
```

in both repos when you’re done.

Let’s ship it! 🚀
