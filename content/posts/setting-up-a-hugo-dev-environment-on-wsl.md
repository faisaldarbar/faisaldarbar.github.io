---
title: "Setting Up a Hugo Dev Environment on WSL"
date: 2025-07-11T02:30:00Z
description: "The Windows Subsystem for Linux"
categories: ["Dev Docs"]
tags: ["hugo", "papermod", "wsl", "yaml", "submodules", "static-site"]

cover:
  image: "/images/setting-up-a-hugo-dev-environment-on-wsl.jpg"
  alt: "Setting Up a Hugo Dev Environment on WSL"
  caption: "Photo by Gabriel Heinzer on Unsplash"

---


## 🚀 Overview

This post is a detailed log of how I set up a local development workflow for the Hugo PaperMod theme — using **WSL on Windows**, cleanly structured folders, **YAML** over TOML, and **Git submodules** for a modular setup. It’s written to serve both as personal documentation and to help anyone trying the same path.

---

## 🧱 Initial Setup

### Why WSL?

I chose WSL (Windows Subsystem for Linux) to get a Unix-like development experience while still working inside Windows. This offers compatibility with tooling, and better support for tools like `git`, `hugo`, and `snap`.

### Steps Taken:

1. **Installed WSL (Ubuntu)**
   - Set up via Microsoft Store
   - Confirmed Linux shell was working properly

2. **Created Project Folder in `/mnt/c/...`**
   - Reason: I wanted to keep source files inside Windows-accessible paths (e.g. `C:\Users\...`) for easier editing with VS Code
   - Folder created: `/mnt/c/Users/faisa/dev/`

---

## ⚙️ Installing Hugo (Extended Edition)

### Why?

PaperMod (and many themes) require the **extended** version of Hugo for SCSS processing.

### Steps:

- Installed via Snap for latest version:
  ```bash
  sudo snap install hugo --channel=extended
  ```

- Verified version:
  ```bash
  hugo version
  ```

---

## 🛠️ Creating the Hugo Site with YAML Config

By default, Hugo uses `TOML`. I wanted to use `YAML` for its readability and preference across other tools.

### Attempt 1:

```bash
hugo new site hugo-papermodest-demo -f yaml
```

This **still created `hugo.toml`** due to older version being used. Eventually I discovered the correct flag:

### ✅ Final Working Command:

```bash
hugo new site hugo-papermodest-demo --format yaml
```

This successfully generated:

```text
hugo.yaml
```

---

## 🎨 Linking the Theme

I cloned a local copy of `hugo-papermodest` for customization.

## 💭 Why Submodules (and Not Just Copying Files)?

Normally when we use a Hugo theme as a submodule, it's part of building a site — and we often copy over files into our site directory to customize things.

But here's the twist — like *Inception* (the movie) — a submodule is a **repo within a repo**.

In my case, I’m not just building a site using a theme — I’m **building and customizing the theme itself** for the first time ever. So:

- I forked the original PaperMod theme  
- Submoduled it into my site’s `themes/` directory  
- I now make **direct changes** to the forked theme  
- And test those changes using the demo site

> 🧑‍💻 This makes me officially a dev now 😄

Eventually, I plan to share my customized theme on GitHub for others to use too.

### Objective:

I didn’t want to copy the theme. I wanted **live updates**: whenever I edit the theme, my demo site should reflect it instantly.

### ✅ Solution: Git Submodules

```bash
git submodule add ../hugo-papermodest themes/hugo-papermodest
```

### ❌ Problem:

Git blocked local file-based submodule links with:
> `fatal: transport 'file' not allowed`

### ✅ Fix:

```bash
git config --local protocol.file.allow always
GIT_ALLOW_PROTOCOL=file git submodule add ../hugo-papermodest themes/hugo-papermodest
```

---

## 🧪 Testing the Site

Started the server with:

```bash
hugo server --buildDrafts
```

Visited: <a href="http://localhost:1313" target="_blank" rel="noopener"><strong>http://localhost:1313</strong></a>

🎉 Site opened successfully!

---

## 📝 First Post & YAML Front Matter

Hugo generated a first post with YAML front matter, like this:

```toml
---
title: 'First Post'
date: 2025-07-12T01:49:00Z
draft: true
---
```

✅ --- delimiters + key: value = YAML

## 🤔 Why Submodules?

- Keeps **theme development and testing isolated**
- Clean Git structure: no need to copy/paste files
- Ensures updates to the theme reflect instantly in the demo site
- Standard Hugo practice for theme development

---

## ✅ Summary

This session helped me:

- Set up a clean and portable Hugo dev environment on WSL
- Choose YAML over TOML for ease
- Understand the role of submodules for theme development
- Get familiar with Hugo's CLI and file structure
- Resolve real-world issues (like file transport errors)

---

## 📌 Next Steps

- Add some custom content to the demo site
- Start modifying the theme’s components inside `hugo-papermodest`
- Push everything to GitHub (main repo and demo separately or with `gh-pages`)

---
