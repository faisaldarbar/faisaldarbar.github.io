---
title: "GitHub Pages Deploy Fix after v3 Deprecation"
date: 2025-01-30T02:30:00Z
description: "GitHub Pages Build and Deployment Workflow Fix for Jekyll Chirpy Theme after v3 Artifact Actions Deprecation"
categories: ["Dev Docs"]
tags: [github, jekyll, chirpy]

cover:
  image: "/images/github-pages-deploy-v3-depracation.webp"
  alt: "GitHub Pages Deploy Fix after v3 Deprecation"
  caption: "Photo by Faisal Darbar on faisaldarbar.com"

---

I recently fixed my GitHub Pages workflow for the **Chirpy Jekyll Theme** after encountering a build failure. This issue was caused by the **deprecation of `actions/upload-artifact v3`**, which GitHub announced w.e.f. January 30, 2025.

### **1. Updated Action Versions**
To stay up to date, I updated my actions to the latest versions:

- `actions/checkout@v4`
- `actions/configure-pages@v4`
- `actions/upload-pages-artifact@v3`

### **2. Fixed `htmlproofer` Command**
The `htmlproofer` command needed an update due to the removal of the `--ignore-urls` option. Here’s the fixed command:

#### **Before:**
```yaml
bundle exec htmlproofer _site   --disable-external   --ignore-urls "/^http:\/\/127.0.0.1/,/^http:\/\/0.0.0.0/,/^http:\/\/localhost/"
```

#### **After:**
```yaml
bundle exec htmlproofer _site   --disable-external   --allow-hash-href
```

### **3. Final Workflow File**
Here’s the updated `pages-deploy.yml` that worked for me:

```yaml
name: "Build and Deploy"
on:
  push:
    branches:
      - main
      - master
    paths-ignore:
      - .gitignore
      - README.md
      - LICENSE

  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.3
          bundler-cache: true

      - name: Build site
        run: bundle exec jekyll b -d "_site${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: "production"

      - name: Test site
        run: |
          bundle exec htmlproofer _site             --disable-external             --allow-hash-href

      - name: Upload site artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "_site${{ steps.pages.outputs.base_path }}"

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### **Conclusion**
With these updates, my build and deployment worked flawlessly again! Keep in mind that **GitHub deprecated `actions/upload-artifact v3`** starting January 30, 2025. Make sure to update your workflows to avoid any interruptions.
