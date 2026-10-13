# DoCraft

> Modern technical guides and cookbook recipes for high-performance systems.

## 🚀 Deployment Modes

This repository is configured to deploy directly to **GitHub Pages** using modern continuous integration.

### Deployment Method 1: Standalone Pure Static Web Page (Zero Jekyll / Ruby Dependency)
- **Fast**: Compiles cleanly with standard Node.js in $< 30\text{s}$.
- **No Ruby**: Zero gem bundle errors, zero Ruby runtime dependencies.
- **Workflow**: Automated build via `.github/workflows/deploy-static.yml`.


```bash
# 1. Install dependencies
npm install

# 2. Run local development preview
npm run dev

# 3. Build standalone static bundle
npm run build
```

### Deployment Method 2: Jekyll SSG (Optional)
If your organization standardizes on Ruby and Jekyll:
```bash
bundle install
bundle exec jekyll serve
```

## 📦 Setting Up GitHub Pages CI/CD
1. Push this repository to GitHub: `https://github.com/ThierryLab/my-docs`.
2. Go to repository **Settings** -> **Pages**.
3. Under **Build and deployment** -> **Source**, select **GitHub Actions**.
4. Every push to the `main` branch will trigger an automated build, link verification, and deployment!
