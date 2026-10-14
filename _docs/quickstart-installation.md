---
layout: guide
title: "Quickstart & Installation"
description: "Get up and running with DocuCraft in less than two minutes across npm, pnpm, yarn, or direct HTML export."
category: "Getting Started"
subcategory: "Fundamentals"
difficulty: "Intermediate"
read_time: "3 min"
last_updated: "October 2026"
tags: ["Installation", "CLI", "Quickstart"]
---

# Quickstart & Installation

Get up and running with DocuCraft in less than two minutes across npm, pnpm, yarn, or direct HTML export.

## Prerequisites

Ensure you have Node.js 18+ installed on your system (or any standard web browser if deploying pure static HTML). No Ruby or Python runtime is required for standalone deployments.



## Creating a New Project

Scaffold a new documentation hub using your preferred package manager or cURL command:

```bash
# Using npm
npx create-docucraft@latest my-docs

# Using pnpm
pnpm create docucraft my-docs

# Using yarn
yarn create docucraft my-docs
```

## Local Development Server

Navigate into your documentation directory and spin up the development environment with hot rebuilds:

```bash
cd my-docs
npm run dev

# Open http://localhost:3000 to preview live documentation
```

## Building for Production

When you are ready to publish, run the build command to generate optimized static files in `/dist` and `/docs`:

```bash
npm run build

# To test the production output locally:
npm run preview
```
