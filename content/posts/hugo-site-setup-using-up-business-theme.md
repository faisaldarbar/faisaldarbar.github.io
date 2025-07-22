---
title: "Hugo Site Setup using Up Business Theme"
date: 2025-07-20T02:30:00Z
description: "This document outlines the process of creating, configuring, and deploying a website using the Up Business Hugo theme."
categories: ["Dev Docs"]
tags: [hugo, up business theme, github pages]

cover:
  image: "/images/hugo-site-setup-using-up-business-theme.png"
  alt: "Hugo Site Setup using Up Business Theme"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

## ✅ Prerequisites

Make sure you have the following installed:

- Hugo (extended version)
- Git
- Node.js and npm
- VS Code or any terminal-based Git interface

---

## 🚀 Create and Set Up the Site

```bash
# Create new site

git clone --branch demo https://github.com/writeonlycode/hugo-up-business.git fsahealthcare
cd fsahealthcare

# Install dependencies
npm install

# Run locally
hugo server
```

You should now be able to view the site at `http://localhost:1313/`.

---

## 🛠 Set Up GitHub Repository

1. **Create a new repository** under your GitHub organization:  
   👉 https://github.com/aptxlabs/fsahealthcare

2. **Enable GitHub Pages**:
   - Go to **Settings** → **Pages**
   - Under **Build and Deployment**, set **Source** to **GitHub Actions**

3. **Initialize Git** in your Hugo site directory:

   ```bash
   cd fsahealthcare
   git init
   git remote add origin https://github.com/aptxlabs/fsahealthcare.git
   ```

4. **Rename the default branch to `main` (if needed):**

   ```bash
   git branch -m main
   ```

5. Update the remote URL

   ```bash
   git remote set-url origin https://github.com/aptxlabs/fsahealthcare.git
   ```

6. Confirm the change

   ```bash
   git remote -v
   ```

   It should now show

   ```bash
   origin  https://github.com/aptxlabs/fsahealthcare.git (fetch)
   origin  https://github.com/aptxlabs/fsahealthcare.git (push)
   ```

7. **Add, commit, and push the source code:**

   ```bash
   git add .
   git commit -m "Initial commit with Hugo site source"
   git push -u origin main
   ```

---

## ⚙️ GitHub Actions: Automatic Deployment

1. Ensure the project already includes `.github/workflows/deploy.yml` (present in demo branch).

2. Update the `hugo.yaml` file:

config > _default > hugo.yaml

```yaml
baseURL: "https://aptxlabs.github.io/fsahealthcare/"
```

3. Update the `deploy.yml` build command:

.gitHub > workflows > deploy.yml

```yaml
# Replace this:
hugo --minify --baseURL "${ steps.pages.outputs.base_url }/"

# With this:
hugo --minify

# Replace this:
push:
    branches: ["demo"]

# With this: 
push:
    branches: ["main"]
```

4. Push the changes:
```bash
git add hugo.yaml .github/workflows/deploy.yml
git commit -m "Fix baseURL and deploy config for GitHub Pages"
git push
```

---

## 🔐 Environment Permissions

1. Go to:
   **Settings → Environments → github-pages** (in the GitHub repo)

2. Under **Deployment branches**, add `main` so GitHub Actions can deploy from it.

3. Run the commit from VS Code.

---

## 🌍 Live Website

Your site is now live at:

👉 <a href="https://fsahealthcare.com" target="_blank" rel="noopener">https://fsahealthcare.com</a>

The site was initially deployed to GitHub Pages at `https://aptxlabs.github.io/fsahealthcare/`, and has since been moved to the custom domain using the following steps:

- **DNS Configuration**:

  - Added the following **A records** to point the apex domain (`fsahealthcare.com`) to GitHub Pages:
    ```
    185.199.108.153
    185.199.109.153
    185.199.110.153
    185.199.111.153
    ```
  - Added a **CNAME record** to point `www.fsahealthcare.com` to:
    ```
    aptxlabs.github.io
    ```

- **GitHub Pages Settings**:

  - Set the **Custom domain** to `fsahealthcare.com`
  - Enabled **Enforce HTTPS**

- **Hugo Configuration**:

  - Updated the `baseURL` in `config/_default/hugo.yaml` to:
    ```yaml
    baseURL: "https://fsahealthcare.com/"
    ```

Once DNS propagation completed, the site became accessible via the custom domain over HTTPS with all resources loading correctly.

