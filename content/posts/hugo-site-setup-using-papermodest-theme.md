---
title: "Hugo Site Setup using PaperModest Theme"
date: 2025-07-08T02:40:00Z
description: "A comprehensive walkthrough of building, customizing, and deploying a Hugo site using the PaperModest theme, including environment setup, Git submodules, and platform transitions."
categories: ["Dev Docs"]
tags: [hugo, papermod, papermodest, wsl, static-site, theme-development, git-submodules, yaml, github-pages, cloudflare-pages, linux, vscode]


cover:
  image: "/images/hugo-site-setup-using-papermodest-theme.webp"
  alt: "Hugo Site Setup using PaperModest Theme"
  caption: "Logo by Steve Francia from Wikimedia Commons, licensed under Apache 2.0"

---

## 🌱 Introduction: The Journey So Far

This project started with me setting up a Hugo blog using the PaperMod theme, developed on a Linux environment and deployed via Cloudflare Pages. I then moved to WSL (Windows Subsystem for Linux) for better flexibility but ran into high I/O issues with VS Code when working across the Windows (`/mnt/c/`) and WSL filesystems. To resolve this, I moved all project files fully into the WSL-native Linux filesystem.

Next, I switched deployment from Cloudflare Pages to GitHub Pages due to DNS conflicts—Cloudflare didn’t support A records (which my custom domain's DNS required), and I already had an existing CNAME for email. This move simplified domain handling significantly.

Later, I also moved from the PaperMod theme to building my own: **PaperModest** — a lightly customized fork tailored for my site. This required cleanly separating the content and theme code into their own repos. Eventually, I published PaperModest on GitHub to give back to the community.

Here’s how everything evolved — step by step, with all the setup, decisions, commands, and learnings.

---

## 🧱 Initial Project Setup (on Linux)

Create a project directory:

```bash
mkdir -p ~/projects/faisaldarbar.com
cd ~/projects/faisaldarbar.com
```

Install Hugo **Extended version** (required for SCSS-based themes like PaperMod):

```bash
sudo apt update
wget https://github.com/gohugoio/hugo/releases/download/v0.147.9/hugo_extended_0.147.9_linux-amd64.deb
sudo apt install ./hugo_extended_0.147.9_linux-amd64.deb
```

Create the Hugo site:

```bash
hugo new site . 
```

Initialize Git and add the PaperMod theme as a submodule:

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

In `hugo.toml`, add:

```toml
theme = "PaperMod"
```

Create your first post:

```bash
hugo new posts/hello-world.md
```

Then edit the front matter (in TOML by default):

```toml
+++
title = 'Hello World'
date = 'YYYY-MM-DDTHH:MM:SS±TZ'
draft = true
+++
```

Preview the site locally:

```bash
hugo server --buildDrafts
```

View at: http://localhost:1313

---

## 🔐 Git & GitHub SSH Setup

Install Git:

```bash
sudo apt update
sudo apt install git
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@example.com"
```

Generate SSH key and add it to GitHub:

```bash
ssh-keygen -t ed25519 -C "your@example.com"
ssh -T git@github.com
```

Push your project to GitHub:

```bash
git remote add origin git@github.com:<your-username>/faisaldarbar.git
git add .
git commit -m "Initial Hugo site with PaperMod theme"
git push -u origin main
```

---

## ☁️ Deploying to Cloudflare Pages (Initial Attempt)

1. Go to [Cloudflare Pages](https://pages.cloudflare.com)
2. Create a new project linked to your GitHub repo
3. Build settings:
   - Framework preset: Hugo
   - Build command: `hugo`
   - Output directory: `public`
4. Update `hugo.toml`:
   ```toml
   baseURL = "https://<your-site>.pages.dev/"
   ```
5. Commit and push changes to fix baseURL

> ⚠️ Later, I moved away from Cloudflare Pages due to limitations with A records for custom domains. GitHub Pages worked better for my needs.

---

## 🖥️ Multi-Platform Workflow: Linux vs WSL

I moved development to WSL for more seamless Hugo support while still using Windows and VS Code for convenience.

### Pros of WSL:

- Full Linux toolchain (bash, apt, git, ssh)
- Better file system consistency with Hugo’s build system
- Native support for scripts, case-sensitive files, and Linux-style permissions

### ⚠️ But I/O from `/mnt/c/` (Windows side) was slow in VS Code

So I **moved all project files into WSL’s Linux filesystem** to solve lag and file sync issues.

---

## 📁 Dev Setup on WSL (with YAML Preference)

Installed Hugo (latest, extended):

```bash
sudo snap install hugo --channel=extended
```

Created the site with YAML config:

```bash
hugo new site hugo-papermodest-demo --format yaml
```

This generated `hugo.yaml` instead of `hugo.toml`.

---

## 🎨 Forking PaperMod → Building PaperModest Theme

I forked PaperMod and began customizing it as **PaperModest**.

Cloned the fork:

```bash
git clone https://github.com/faisaldarbar/hugo-papermodest.git
```

Added it to my demo site as a local Git submodule:

```bash
git submodule add ../hugo-papermodest themes/hugo-papermodest
```

### 🐛 Submodule Error Fix

Git blocks local file-based submodules by default. Fix:

```bash
git config --local protocol.file.allow always
GIT_ALLOW_PROTOCOL=file git submodule add ../hugo-papermodest themes/hugo-papermodest
```

Updated `hugo.yaml`:

```yaml
theme: hugo-papermodest
```

---

## 📂 Migrating Site Content & Theme Changes

I wanted a clean separation between **theme** and **site content**.

### 🔁 Copy Content Only

From the old repo (`faisaldarbar.github.io`), copy:

- `content/`
- `static/`
- `assets/`
- `config/`

**Don’t copy theme files** — they now live in `themes/hugo-papermodest`.

### ✨ Theme Customizations

Any layout or component change (e.g., modifying `baseof.html`, adding partials) must be made inside the **theme repo**, not the site repo.

---

## 🚀 Relaunch & Clean Deployment

- Renamed the demo site folder ➝ `faisaldarbar.github.io`
- Pushed the updated theme and content to their respective GitHub repos
- Site now uses my custom theme and is version-controlled with submodules

---

## 📦 Open-Sourcing the PaperModest Theme

I published `hugo-papermodest` on GitHub.

Install command:

```bash
git submodule add https://github.com/faisaldarbar/hugo-papermodest themes/hugo-papermodest
```

In your config file:

```yaml
theme: hugo-papermodest
```

This theme is lightly customized and **not actively maintained**. Use it freely, but at your own risk.

---


## 🧠 Useful Git & Hugo Commands (Cheat Sheet Style)

Sometimes it's the simple commands we forget — especially when switching machines or working across WSL and Linux setups. Here are a few I always come back to:

### 🔁 After Cloning a Repo

If your theme is a Git submodule (like `PaperModest`), remember to initialize it after cloning:

```bash
git submodule update --init --recursive
```

> 💡 I move between machines often — this command saves me from missing theme files when I switch environments.

---

### 🧪 Start Local Hugo Dev Server (with Drafts)

This ensures that local preview uses a clean localhost baseURL — essential when your live site is already using a production domain:

```bash
hugo server -D --baseURL=http://localhost:1313/
```

> 💡 Especially important when working with themes that include canonical URLs or social sharing metadata.

---

## 📘 How I’d Set Up a Hugo Site Today (with PaperModest)

### 1. Use WSL (Ubuntu) for local dev  
Avoid `/mnt/c/`, work inside WSL-native paths

### 2. Install Hugo (Extended) via Snap

```bash
sudo snap install hugo --channel=extended
```

### 3. Create Site with YAML Config

```bash
hugo new site mysite --format yaml
```

### 4. Add PaperModest Theme via Submodule

```bash
git submodule add https://github.com/faisaldarbar/hugo-papermodest themes/hugo-papermodest
```

In `hugo.yaml`:

```yaml
theme: hugo-papermodest
```

### 5. Add Content, Update Config, Run Local Preview

```bash
hugo new posts/my-first-post.md
hugo server --buildDrafts
```

### 6. Push to GitHub (SSH Recommended)

```bash
git init
git remote add origin git@github.com:yourname/mysite.git
git add .
git commit -m "Initial site"
git push -u origin main
```

### 7. Deploy via GitHub Pages or Netlify  
Prefer GitHub Pages for simplicity if you control the domain DNS (A records, CNAMEs, etc.)

---

## 🙌 Final Thoughts

This project was a deep dive into static site generators, cross-platform workflows, and theme development. PaperModest is the outcome of that effort — simple, minimal, and built to serve my writing. If it helps you too, I’m glad I shared it.