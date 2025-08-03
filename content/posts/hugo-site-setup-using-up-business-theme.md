---
title: "Hugo Site Setup using Up Business Theme"
date: 2025-07-20T02:30:00Z
description: "This document outlines the process of creating, configuring, and deploying a website using the Up Business Hugo theme."
categories: ["Dev Docs"]
tags: [hugo, up business theme, github pages]

cover:
  image: "/images/hugo-site-setup-using-up-business-theme.webp"
  alt: "Hugo Site Setup using Up Business Theme"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

## ✅ Prerequisites

Make sure you have the following installed:

### Hugo (extended version)

Always update your packages before an install.

```bash
sudo apt update
```
Download and install the extended version of Hugo

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.147.9/hugo_extended_0.147.9_linux-amd64.deb
sudo apt install ./hugo_extended_0.147.9_linux-amd64.deb
```
### Git

```bash
sudo apt update
sudo apt install git
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@example.com"
```

### Node.js and npm

Instead of using apt, which installs outdated versions, use NodeSource to install the current LTS version (more stable for dev work):

```bash
# Add NodeSource repository for Node 20 (LTS as of mid-2025)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```
This installs both node and npm.

Verify Installation

```bash
node -v
npm -v
```
From your project root

```bash
npm install
```
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

After setting up and deploying the Hugo site using the **Up Business** theme, we implemented an important improvement to ensure **long-term stability** and **independence** from the original theme developer.

### ✅ What We Did

We **vendored the theme** using Hugo Modules, meaning we pulled the full theme source code into our project under a local `_vendor/` directory using the command:

```bash
hugo mod vendor
```

We then added `vendored: true` under `module:` to the **root-level** `hugo.yaml` configuration (since we have two of those files in the repo) to tell Hugo to use the local vendored copy:

```yaml
module:
  hugo_version:
    min: "0.147.8"
  
  vendored: true

  imports:
    - path: github.com/writeonlycode/hugo-up-business
```

Finally, we committed the entire `_vendor/` folder to our Git repository to make the site self-contained.

### 🧠 Why We Did It

By default, Hugo Modules (including themes) are fetched from **external Git repositories** during the build process. This introduces a dependency on the **availability** and **stability** of those upstream sources.

Even though we were using a pinned version of the theme (via `go.mod`), edge cases remained where:

- The theme repo could be deleted from Github
- GitHub or the Go proxy could be temporarily unavailable
- A clean machine or CI runner might not be able to fetch the module

### ✅ Benefits of Vendoring

By vendoring the theme:

- ✅ We **removed all reliance** on the upstream Git repo
- ✅ The site now **builds and runs fully offline**
- ✅ We're protected from **theme updates or deletions**
- ✅ CI/CD and production builds are **isolated and reproducible**
- ✅ Even with `go.mod` and `go.sum` deleted, the site continues to work — proving that `_vendor/` is truly self-sufficient

This makes the website **future-proof** and ensures that it will continue working exactly as expected — even years from now, regardless of what happens to the original theme project.

### 🧪 How We Verified It

We ran the following test:

1. Deleted `go.mod` and `go.sum`
   
   ```bash
   rm go.mod go.sum
   ```

2. Cleared all Hugo and Go caches:
   
   ```bash
   rm -rf ~/.cache/hugo
   rm -rf ~/.cache/go-build
   go clean -modcache
   ```

3. Deleted `public/`, `resources/`, and `.hugo_build.lock`
   
   ```bash
   rm -rf public/ resources/ .hugo_build.lock
   ```

4. Disconnected from the internet

   ```bash
   ping google.com
   ```

5. Ran:

   ```bash
   hugo server
   ```

✅ The site **built and served correctly**, confirming that **all required code and assets** were available locally in `_vendor/`.

---

### 🤩 Final Thoughts

This step isn't mandatory for every Hugo project, but it’s **highly recommended** for:

- Production deployments
- Long-term archival
- Projects where upstream stability is a concern

By vendoring, we’ve **eliminated a potential point of failure** and taken full ownership of our project stack — making the website truly **future-proof**.




