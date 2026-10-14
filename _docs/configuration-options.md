---
layout: guide
title: "Configuration & Customization"
description: "Deep dive into configuration parameters, custom typography tokens, brand color schemes, and search indexing."
category: "Core Concepts"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["Configuration", "Theming", "SEO"]
---

# Configuration & Customization

Deep dive into configuration parameters, custom typography tokens, brand color schemes, and search indexing.

## Configuration Schema

The primary configuration file controls brand metadata, navigation trees, and deployment behavior:

```typescript
import { defineConfig } from '@docucraft/config';

export default defineConfig({
  title: 'Enterprise Developer Hub',
  description: 'Official API guides, SDK reference, and architecture specs.',
  author: 'Platform Engineering',
  github: {
    owner: 'enterprise-org',
    repo: 'docs',
    branch: 'main',
  },
  theme: {
    accentColor: '#2563EB', // Royal Blue
    defaultMode: 'dark',    // 'light' | 'dark' | 'system'
    fontDisplay: 'Plus Jakarta Sans',
    fontBody: 'DM Sans',
    fontMono: 'JetBrains Mono',
  },
  search: {
    fuzzyThreshold: 0.35,
    maxResults: 8,
    includeCodeSnippets: true,
  },
  deployment: {
    target: 'static', // 'static' or 'jekyll'
    basePath: './',
  },
});
```

## Typography & Token System

DocuCraft uses standard CSS custom properties. You can customize your palette in `src/index.css` to align with your organization’s design tokens without breaking dark mode compensation.


