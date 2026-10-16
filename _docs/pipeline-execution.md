---
layout: guide
title: "Execution Pipeline & Lifecycle"
description: "Explore the request lifecycle, caching layers, middleware hooks, and AST tokenization pipeline."
classification: "cat101"
category: "Core Concepts"
subcategory: "Architecture"
difficulty: "Intermediate"
read_time: "4 min"
last_updated: "October 2026"
tags: ["Pipeline", "Lifecycle", "Middleware"]
---

# Execution Pipeline & Lifecycle

Explore the request lifecycle, caching layers, middleware hooks, and AST tokenization pipeline.

## Lifecycle Stages

Every request transitions through four deterministic lifecycle phases:
1. **Routing & Match Resolution**: URL slug is matched against the inverted index.
2. **Middleware Evaluation**: Custom headers, auth guards, and telemetry run.
3. **Token AST Hydration**: Code blocks are parsed into syntax-colored spans.
4. **Viewport Render**: Breadcrumbs, TOC observer, and reading progress hydrate.



## Custom Middleware Example

Register custom middleware in the pipeline to log telemetry or rewrite paths:

```typescript
export const auditMiddleware = (req: any, next: () => any) => {
  const start = Date.now();
  console.log(`[DocuCraft] Navigating to: ${req.slug}`);
  const result = next();
  console.log(`[DocuCraft] Rendered in ${Date.now() - start}ms`);
  return result;
};
```
