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

git clone --branch demo https://github.com/writeonlycode/hugo-up-business.git aptxlabs
cd aptxlabs

# Install dependencies
npm install

# Run locally
hugo server
```

You should now be able to view the site at `http://localhost:1313/`.

---

## 🛠 Set Up GitHub Repository

1. **Create a new repository** under your GitHub organization:  
   👉 https://github.com/aptxlabs/aptxlabs

2. **Enable GitHub Pages**:
   - Go to **Settings** → **Pages**
   - Under **Build and Deployment**, set **Source** to **GitHub Actions**

3. **Initialize Git** in your Hugo site directory:

   ```bash
   cd aptxlabs
   git init
   git remote add origin https://github.com/aptxlabs/aptxlabs.git
   ```

4. **Rename the default branch to `main` (if needed):**

   ```bash
   git branch -m main
   ```

5. Update the remote URL

   ```bash
   git remote set-url origin https://github.com/aptxlabs/aptxlabs.git
   ```

6. Confirm the change

   ```bash
   git remote -v
   ```

   It should now show

   ```bash
   origin  https://github.com/aptxlabs/aptxlabs.git (fetch)
   origin  https://github.com/aptxlabs/aptxlabs.git (push)
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
baseURL: "https://aptxlabs.github.io/aptxlabs/"
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

👉 <a href="https://aptxlabs.github.io/aptxlabs/" target="_blank" rel="noopener">Aptxlabs</a>

The site was initially deployed to GitHub Pages at `https://aptxlabs.github.io/aptxlabs/`, and has since been moved to the custom domain using the following steps:

- **DNS Configuration**:

  - Added the following **A records** to point the apex domain (`aptxlabs.com`) to GitHub Pages:
    ```
    185.199.108.153
    185.199.109.153
    185.199.110.153
    185.199.111.153
    ```
  - Added a **CNAME record** to point `www.aptxlabs.com` to:
    ```
    aptxlabs.github.io
    ```

- **GitHub Pages Settings**:

  - Set the **Custom domain** to `aptxlabs.com`
  - Enabled **Enforce HTTPS**

- **Hugo Configuration**:

  - Updated the `baseURL` in `config/_default/hugo.yaml` to:
    ```yaml
    baseURL: "https://aptxlabs.com/"
    ```

Once DNS propagation completed, the site became accessible via the custom domain over HTTPS with all resources loading correctly.

## 🛡️ Securing the Website Against External Dependency Failures

After setting up and deploying the Hugo site using the Up Business theme, we implemented an important improvement to ensure long-term stability and independence from the original theme developer.

### ✅ What We Did

We **vendored** the theme using Hugo Modules, which means we pulled the full theme source code into our project under a local `_vendor/` directory. This was done with the command:

```bash
hugo mod vendor
```

We then added this to the **root-level** `hugo.yaml` configuration (since we have two of those files in the repo) to tell Hugo to use the local vendored copy:

```yaml
module:
  vendored: true
```

Finally, we committed the entire `_vendor/` folder to our Git repository to make the site self-contained.

### 🧠 Why We Did It

By default, Hugo modules (like themes) are pulled from external Git repositories during the build process. This creates a dependency on the availability and integrity of those repos.

While we were already using a pinned version of the theme (via Go Modules), there were still edge cases where the theme being deleted from GitHub — or the Go proxy failing — could cause a future clean build to fail.

By vendoring the theme:

- ✅ We removed all reliance on the upstream theme repository.
- ✅ The site builds and runs completely offline.
- ✅ We are protected from upstream changes, deletions, or GitHub outages.
- ✅ Our production deployments (e.g. via GitHub Actions) are now fully isolated and stable.

This makes the website **future-proof** and ensures that it will continue working exactly as expected — even years from now, regardless of what happens to the original theme project.

### 🧪 How We Verified It

After vendoring, we ran:

```bash
hugo server
```

— while disconnected from the internet — and confirmed that the site loaded correctly. This validated that all assets and theme logic are being served locally, not fetched from the web.

---

This additional step isn’t required for all projects, but it's **highly recommended for production sites** or any site where long-term reliability matters.

By making this change, we've eliminated a potential point of failure and taken full ownership of our project stack.



