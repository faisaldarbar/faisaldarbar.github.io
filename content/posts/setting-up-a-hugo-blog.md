---
title: "Setting Up a Hugo Blog with PaperMod"
date: 2025-07-07T02:30:00Z
description: "This guide documents the setup used for deploying a Hugo blog with the PaperMod Theme"
categories: ["Dev Docs"]
tags: [hugo, papermod]

cover:
  image: "/images/hugo.png"
  alt: "Setting Up a Hugo Blog with PaperMod"
  caption: "Logo by Steve Francia from Wikimedia Commons, licensed under Apache 2.0"

---

This guide documents the exact setup used for deploying a Hugo blog with the PaperMod theme to Cloudflare Pages, with version control via GitHub (SSH).

## 🧱 1. Directory Setup

```bash
mkdir -p ~/projects/faisaldarbar.com
cd ~/projects/faisaldarbar.com
```

---

## 🔧 2. Install Hugo Extended (Linux)

Download and install the **extended** version of Hugo (required by some themes like PaperMod):

```bash
sudo apt update
```
Always update your packages before an install.

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.147.9/hugo_extended_0.147.9_linux-amd64.deb
sudo apt install ./hugo_extended_0.147.9_linux-amd64.deb
```

---

## 📂 3. Create a New Hugo Site

Inside the project folder:

```bash
hugo new site .
```

This creates the Hugo directory structure in-place.

---

## 🎨 4. Add PaperMod Theme (Using Git Submodule ✅)

Add PaperMod theme as a Git submodule:

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

Update `hugo.toml`:

```toml
theme = "PaperMod"
```

---

## 📜 5. Create Your First Post

```bash
hugo new posts/hello-world.md
```

Edit `content/posts/hello-world.md`:

```toml
+++
title = 'Hello World'
date = 'YYYY-MM-DDTHH:MM:SS±TZ'
draft = true
+++
```

---

## 🥪 6. Run the Local Server

```bash
hugo server --buildDrafts
```

Preview at:\
<a href="http://localhost:1313" target="_blank" rel="noopener"><strong>http://localhost:1313</strong></a>

---

## 🛠️ 7. Set Up GitHub Repository

### ✅ Git Configuration

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

---

## 🔐 8. Set Up GitHub SSH Access

### Generate SSH Key

```bash
ssh-keygen -t ed25519 -C "your@example.com"
```

Accept the default path. Then add the public key to GitHub via:\
**GitHub > Settings > SSH and GPG keys > New SSH Key**

### Test Connection

```bash
ssh -T git@github.com
```

Expected:

> "Hi username! You've successfully authenticated..."

---

## ☁️ 9. Push Site to GitHub

Create a new repo (e.g., `faisaldarbar`), then:

```bash
git remote add origin git@github.com:<your-username>/faisaldarbar.git
git add .
git commit -m "Initial Hugo site with PaperMod theme"
git push -u origin main
```

---

## 🌐 10. Deploy to Cloudflare Pages

1. Go to <a href="https://pages.cloudflare.com/" target="_blank" rel="noopener">Cloudflare Pages</a>
2. Click **Create Project**
3. Authorize GitHub and select your repo
4. Build settings:
   - **Framework preset**: Hugo
   - **Build command**: `hugo`
   - **Output directory**: `public`
5. Deploy 🚀

Live URL:\
**https\://****.pages.dev**

---

## 🔧 11. Fix Links & Base URL

Update `hugo.toml`:

```toml
baseURL = "https://<your-site>.pages.dev/"
```

Commit and push:

```bash
git add .
git commit -m "Fix baseURL for Cloudflare Pages"
git push
```

---

## 📋 12. GitHub Actions Taken

- ✅ Created repo (`faisaldarbar`)
- ✅ Added SSH key to GitHub
- ✅ Authorized GitHub → Cloudflare Pages
- ✅ Configured SSH authentication (avoided storing token)
- ✅ Resolved Git submodule issue by proper theme addition

---

## 🧑‍💻 13. Set Up VS Code on Linux (Optional but Recommended)

Install Visual Studio Code (non-snap method):

```bash
sudo apt update
sudo apt install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/
sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/packages.microsoft.gpg] \
https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

📦 Recommended Extensions
Install these extensions for better Hugo development:

- **Hugo Language Support** – Syntax highlighting for Hugo templates

- **Markdown All in One** – Enhances Markdown editing

- **YAML** – Syntax support for .yaml/.toml config files

- **GitLens** – Advanced Git history and insights

---

## 🖥️ 14. Multi-Platform Workflow (Linux + Windows)

This project can be managed from both **Linux** and **Windows** environments. However, some operations are more stable or predictable in Linux due to Hugo’s tooling and file system differences.

### ✅ Tasks Safe to Do on Windows (VS Code)

- Writing/editing Markdown posts
- Creating new posts with `hugo new posts/title.md`
- Running local preview server: `hugo server --buildDrafts`
- Committing and pushing changes via VS Code
- Previewing site locally
- Updating `hugo.toml`, front matter, and minor config changes

> Windows Git via VS Code handles basic operations well and securely without requiring manual SSH setup.

---

### ⚠️ Tasks Best Done on Linux

| Task | Why Use Linux |
|------|----------------|
| Theme updates (`git submodule update`) | Git submodules can behave inconsistently on Windows |
| Running shell scripts (`*.sh`) | Native support in Linux |
| Advanced Hugo tasks (`hugo mod`, theme asset builds) | More predictable behavior and compatibility |
| Fixing case-sensitive file issues | Linux file systems are case-sensitive (unlike Windows) |
| Managing SSH credentials securely | Linux has better terminal-based SSH tooling |
| CI/CD troubleshooting (e.g., Cloudflare build failures) | Closer match to Linux-based build servers |

If something breaks silently in Windows, validate it in Linux:

```bash
hugo server --buildDrafts
```

## ✅ Setup Summary

| Feature             | Status          |
| ------------------- | --------------- |
| Hugo Installed      | ✅               |
| PaperMod Theme      | ✅ via submodule |
| GitHub Repo         | ✅ via SSH       |
| Cloudflare Pages    | ✅ deployed      |
| Custom baseURL      | ✅ fixed         |
| Token Prompts Fixed | ✅ via SSH       |

