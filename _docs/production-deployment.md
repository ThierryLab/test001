---
layout: guide
title: "GitHub Pages & Continuous Integration"
description: "Comprehensive guide to zero-downtime GitHub Actions deployments, branch publishing, and automated builds."
category: "Deployment & Operations"
subcategory: "GitHub Pages"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["Deployment", "GitHubPages", "CI/CD"]
---

# GitHub Pages & Continuous Integration

Comprehensive guide to zero-downtime GitHub Actions deployments, branch publishing, and automated builds.

## GitHub Pages Automated CI Workflow

The official `.github/workflows/deploy-static.yml` workflow compiles the application and publishes the static artifacts directly to GitHub Pages with zero Ruby dependencies:

```bash
# Triggered on every push to main
name: Deploy Static Documentation to GitHub Pages
on:
  push:
    branches: ["main"]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'
```

## Avoiding the Common 404 Pitfall

In your repository settings under **Settings** -> **Pages**, make sure to set the **Source** to **GitHub Actions** rather than "Deploy from a branch". The included `.nojekyll` file ensures directory paths and underscore assets are served without Jekyll intercepting them.


