---
layout: guide
title: "Standalone Static Web Export"
description: "How to build and distribute pure, zero-dependency HTML/CSS/JS bundles for internal intranet or air-gapped systems."
category: "Deployment & Operations"
difficulty: "Intermediate"
read_time: "3 min"
last_updated: "October 2026"
tags: ["StaticExport", "ZeroLockin", "Offline"]
---

# Standalone Static Web Export

How to build and distribute pure, zero-dependency HTML/CSS/JS bundles for internal intranet or air-gapped systems.

## Self-Contained Bundle Generation

When you click "Export Site" in the top bar or run `npm run build`, DocuCraft generates an entirely self-contained distribution folder. Every asset, script, font, and search index is packaged so the documentation can even be opened directly via `file:///` protocol in secure corporate environments.


