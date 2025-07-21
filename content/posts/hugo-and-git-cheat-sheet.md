---
title: "Hugo & Git Commands Cheat Sheet"
date: 2025-07-13T02:31:00Z
description: "A living list of commands I use while working on my Hugo blog"
categories: ["Dev Docs"]
tags: ["hugo", "git", "workflow", "cheatsheet"]

cover:
  image: "/images/hugo-and-git-cheat-sheet.png"
  alt: "Hugo & Git Commands Cheat Sheet"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"
---

Sometimes we forget the simple things. Here's a living list of commands I use while working on my Hugo blog with a custom theme and GitHub workflow.

---

## 🧱 After Cloning the Repo

Background: I keep switching local locations for my github repos. From one laptop to another. WSL to Linux. This command is very useful.

**Initialize Git submodules (for theme):**
Cloning the repo does not automatically fetch submodule content — including your theme.

```bash
git submodule update --init --recursive
```

---

## 🧪 Local Development Server

Background: The baseURL specified in the main config file is a real issue if your website is already in production. 

**Start the Hugo development server**
Includes draft content and sets localhost as the base URL for correct local links.

```bash
hugo server -D --baseURL=http://localhost:1313/
```

---

More coming soon.

Feel free to copy, fork, or steal this list — I made it for myself 😎
