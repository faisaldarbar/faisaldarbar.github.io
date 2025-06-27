---
layout: post
title: "Theme-Aware Images in Jekyll"
subtitle: "How to Add Theme-Aware Images in Jekyll"
date: 2025-06-26 18:00:00 +0300
categories: [Web Development, HTML/CSS]
tags: [dark mode, light mode, responsive images]
image: /images/theme-aware-images-in-jekyll-light.png  # Used for SEO/meta
theme_images:
  light: /images/theme-aware-images-in-jekyll-light.png
  dark: /images/theme-aware-images-in-jekyll-dark.png
pin: no
---

Disclaimer: This is currently undergoing testing.

<picture>
  <source srcset="{{ page.theme_images.dark }}" media="(prefers-color-scheme: dark)">
  <source srcset="{{ page.theme_images.light }}" media="(prefers-color-scheme: light)">
  <img src="{{ page.theme_images.light }}" alt="Theme-aware image" width="600" height="315" loading="lazy">
</picture>



Making your website more visually consistent with your visitors' system preferences is a small touch that leaves a big impact. In this post, you'll learn how to create **theme-aware images** — images that automatically switch between **light and dark mode** depending on the user's browser or OS setting.

---

## 🌓 What Are Theme-Aware Images?

A theme-aware image responds to your site’s current color scheme. For example:

- Light version is shown when the user is in **light mode**
- Dark version is shown when the user is in **dark mode**

This enhances visibility, design harmony, and overall polish. It also aligns beautifully with the **Jekyll Chirpy** theme’s built-in dark/light toggle.

---

## ✅ How It Works

We use the HTML `<picture>` element along with a `media` query that detects the user's theme preference:

```html
<picture>
  <source srcset="/images/theme-aware-images-in-jekyll-dark.png" media="(prefers-color-scheme: dark)">
  <img src="/images/theme-aware-images-in-jekyll-light.png" alt="A theme-aware example image" style="box-shadow: 0 4px 10px rgba(0,0,0,0.15); border-radius: 8px;">
</picture>
```

- The `<source>` tag loads the dark version when dark mode is active.
- The `<img>` tag serves the default (light) version.
- The style adds some nice visual polish (shadow, rounding).

---

## 🛠 Step-by-Step Instructions

### 1. Create Two Versions of the Image

Design or export your image in two versions:

| Type       | File                            | Notes                         |
|------------|----------------------------------|-------------------------------|
| Light mode | `/images/theme-aware-images-in-jekyll-light.png` | Used by default               |
| Dark mode  | `/images/theme-aware-images-in-jekyll-dark.png`  | Triggered by dark theme       |

Both images should be the **same dimensions** (e.g. 600×315 px), and optimized for web.

---

### 2. Use the HTML Snippet in Your Markdown

Place this directly into your `.md` post body (Jekyll supports raw HTML in markdown):

```html
<picture>
  <source srcset="/images/theme-aware-images-in-jekyll-dark.png" media="(prefers-color-scheme: dark)">
  <img src="/images/theme-aware-images-in-jekyll-light.png" alt="A theme-aware example image" style="box-shadow: 0 4px 10px rgba(0,0,0,0.15); border-radius: 8px;">
</picture>
```

You can copy this block anywhere in your content. It works automatically with no JavaScript required.

---

### 3. Add the Images to the Front Matter

This enables Chirpy to show the correct image on post previews, blog cards, and Open Graph previews:

```yaml
image:
  light: /images/theme-aware-images-in-jekyll-light.png
  dark: /images/theme-aware-images-in-jekyll-dark.png
```

Just like we've done in the front matter at the top of this post.

---

## 👁‍🗨 Live Preview Test

Try switching your browser or system between dark and light mode and watch the image below update automatically:

<picture>
  <source srcset="/images/theme-aware-images-in-jekyll-dark.png" media="(prefers-color-scheme: dark)">
  <img src="/images/theme-aware-images-in-jekyll-light.png" alt="Test: Theme-aware image preview" style="width: 100%; max-width: 600px; box-shadow: 0 4px 10px rgba(0,0,0,0.15); border-radius: 8px;">
</picture>

---

## ✅ Conclusion

Using `<picture>` and `prefers-color-scheme`, you can easily serve theme-specific images that improve readability and user experience — with no extra libraries or scripts.

Now your posts and previews can be as smart about themes as your site is!

---

Got questions or want help setting this up in your own Jekyll project? Drop a comment or contact me!

