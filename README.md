# THE DOC MASTER

> Enterprise technical documentation platform with interactive navigation tree, multi-language snippets, and zero-dependency static deployment.

## 🚀 Deployment Modes

### Mode 1: Standalone Static Web Page (Zero Jekyll / Ruby Dependency)
- No Ruby, no Bundler, no gem install required.
- Standard Node.js static build in $< 30\text{s}$.
- Deploys via `.github/workflows/deploy-static.yml` directly to GitHub Pages.

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build standalone production bundle
npm run build
```

### Mode 2: Jekyll SSG (Optional)
If you prefer Jekyll and Ruby:
```bash
bundle install
bundle exec jekyll serve
```

## 📦 Fixing the "404 File Not Found" on GitHub Pages
1. Go to your GitHub repository -> **Settings** -> **Pages**.
2. Under **Build and deployment** -> **Source**, select **GitHub Actions** (NOT "Deploy from a branch").
3. Push your repository to GitHub.
4. Click the **Actions** tab in GitHub and verify the workflow completes with a green checkmark.
5. Your site is live at: https://ThierryLab.github.io/test001/
