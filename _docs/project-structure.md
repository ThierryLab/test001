---
layout: guide
title: "Project Structure & Files"
description: "Learn how to structure multi-chapter technical documentation, assets, navigation definitions, and layout components."
category: "Getting Started"
difficulty: "Intermediate"
read_time: "3 min"
last_updated: "October 2026"
tags: ["Organization", "Architecture", "Folders"]
---

# Project Structure & Files

Learn how to structure multi-chapter technical documentation, assets, navigation definitions, and layout components.

## Directory Hierarchy

DocuCraft follows a clean, modular structure designed for scale and enterprise reliability:

```bash
my-docs/
├── .github/
│   └── workflows/
│       └── deploy-static.yml   # GitHub Pages automated CI/CD
├── src/
│   ├── components/            # Sidebar, Header, CodeBlock, TOC
│   ├── data/                  # Typed documentation articles & hierarchy
│   ├── types/                 # Schema definitions
│   └── utils/                 # Syntax highlighter & search engine
├── public/
│   ├── .nojekyll              # Prevents GitHub from triggering Jekyll
│   └── 404.html               # Clean SPA fallback
├── _layouts/                  # Universal layouts for Jekyll export
├── package.json
└── vite.config.ts             # Relative base configuration
```

## Adding New Documentation Pages

Articles can be authored in TypeScript or standard Markdown. Each page includes an ID, title, URL slug, read time, category, and structured content sections.


