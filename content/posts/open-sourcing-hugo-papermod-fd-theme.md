---
title: "Open-Sourcing Hugo PaperMod-FD Theme"
date: 2025-07-11T12:00:00Z
draft: false
categories: ["Dev Docs"]
tags: ["hugo theme", "papermod", "papermod-fd", "open source"]
---


## Objective

Open source and document my custom Hugo theme `PaperMod-FD`, ensuring it's usable by others but clearly marked as not actively maintained. My primary goal remains focused on writing for my main site, but I'm sharing this theme as a way of giving back.

---

## ✅ Tasks

### 1. Finalize Git Submodule in Live Site

Ensure my live site correctly includes the theme submodule.

```bash
git add .gitmodules themes/hugo-papermod-fd
git commit -m "Add papermod-fd theme as submodule"
```

### 2. Theme Naming & Folder Structure

- **Repo name:** `hugo-papermod-fd`
- **Theme name:** `PaperMod-FD`
- **Theme folder:** `themes/hugo-papermod-fd`

### 3. Add `theme.toml`

```toml
name = "PaperMod-FD"
license = "MIT"
licenselink = "https://github.com/faisaldarbar/hugo-papermod-fd/blob/main/LICENSE"
description = "A lightly customized fork of PaperMod for my personal site. Shared for others, but not actively maintained."
homepage = "https://github.com/faisaldarbar/hugo-papermod-fd"
tags = ["blog", "minimal", "fast", "responsive", "dark mode", "personal"]
features = ["dark mode", "light mode", "optimized for reading"]
min_version = "0.100.0"

[author]
  name = "Faisal Darbar"
  homepage = "https://faisaldarbar.com"
```

### 4. Update License

Prepend this notice to the existing MIT `LICENSE` file:

```text
This is a fork of the PaperMod theme.
Modifications made by Faisal Darbar – https://faisaldarbar.com

The original project is licensed under the MIT license (below).
```

### 5. Add README

Structure:

````md
# PaperMod-FD

A lightweight, slightly customized fork of the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) Hugo theme — built and tailored for my personal website [faisaldarbar.com](https://faisaldarbar.com).

## 🚧 Status
This theme works as of now, but I actively tweak it for my own needs. It may break without notice.  
**Please use it only if you're comfortable with that.** No support or contributions are expected.

## ✨ Differences from PaperMod
- Slight layout tweaks
- TBA

## 🧪 Demo (Planned)
[https://faisaldarbar.github.io/hugo-papermod-fd/](https://faisaldarbar.github.io/hugo-papermod-fd/)

## 🔧 Usage
```bash
git submodule add https://github.com/faisaldarbar/hugo-papermod-fd themes/hugo-papermod-fd
````

In the config file:

```yaml
theme: hugo-papermod-fd
```

## 📜 License

MIT – original theme by [Aditya Telange](https://github.com/adityatelange)

````

### 6. Create Demo Site (Optional)
- Set up a `gh-pages` branch to host a live demo
- Or create a new repo `papermod-fd-demo` to host it separately

### 7. Add Disclaimer
In `README.md`, add:
```md
## 🙏 Disclaimer

This is shared in the spirit of open source, just like I benefited from others' work.  
I’m not maintaining it actively, and I’m not accepting issues or contributions for now.  
Use it freely, but at your own risk.
````

Also update repo settings:

- Disable issues (optional)
- Add "archived", "no-contributions", or "experimental" topic tags

### 8. Add Badges (Later)

```md
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hugo Version](https://img.shields.io/badge/hugo-0.121.2-blue)](https://gohugo.io)
[![Theme Status](https://img.shields.io/badge/status-experimental-orange)]()
```

### 9. .gitignore

Ensure this is present:

```gitignore
public/
resources/
node_modules/
```

---

## 🏁 Goal Definition

**Goal:** By end of Week 28, my `PaperMod-FD` theme should be:

- 📦 Packaged and documented for public use
- 🪪 Clearly marked as not maintained
- 🧪 Optionally viewable via a demo site
- 🎁 Ready for anyone to fork and use

