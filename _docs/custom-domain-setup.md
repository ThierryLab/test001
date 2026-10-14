---
layout: guide
title: "Custom Domains, SSL & DNS"
description: "Configure custom domains like docs.yourcompany.com with automatic Let’s Encrypt SSL and CDN acceleration."
category: "Deployment & Operations"
difficulty: "Intermediate"
read_time: "4 min"
last_updated: "October 2026"
tags: ["DNS", "CustomDomain", "SSL"]
---

# Custom Domains, SSL & DNS

Configure custom domains like docs.yourcompany.com with automatic Let’s Encrypt SSL and CDN acceleration.

## CNAME Configuration

To attach a custom domain like `docs.company.com`, add a `CNAME` file to your `public/` directory with your hostname. Then configure your DNS provider with a CNAME record pointing to `<username>.github.io`.



## Enforcing HTTPS

Once DNS propagation completes (typically 2-10 minutes), check "Enforce HTTPS" in your GitHub Pages settings to automatically provision an SSL certificate.


