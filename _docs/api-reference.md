---
layout: guide
title: "REST & RPC API Reference"
description: "Complete specification of developer endpoints, request validation schemas, and standard error envelopes."
category: "API Reference"
difficulty: "Intermediate"
read_time: "6 min"
last_updated: "October 2026"
tags: ["API", "Endpoints", "Schemas"]
---

# REST & RPC API Reference

Complete specification of developer endpoints, request validation schemas, and standard error envelopes.

## Base URLs & Versioning

All API requests must be issued over HTTPS to the active cluster endpoint. API versions are expressed directly within the URI path:

```bash
Production:  https://api.docucraft.dev/v1
Staging:     https://staging-api.docucraft.dev/v1
```

## Authentication Header

Authenticate all requests using a standard HTTP Bearer token in the `Authorization` header:

```curl
curl -X GET https://api.docucraft.dev/v1/projects \
  -H "Authorization: Bearer sec_live_9f81a7b8e" \
  -H "Content-Type: application/json"
```

## Standard Response Schema

All API responses follow a uniform JSON envelope with request metadata and latency telemetry:

```json
{
  "status": "success",
  "data": {
    "id": "proj_9921b",
    "name": "DocuCraft Core",
    "region": "us-west-2",
    "activeDeployments": 3,
    "createdAt": "2026-10-13T12:00:00Z"
  },
  "meta": {
    "requestId": "req_88192a",
    "latencyMs": 14.2
  }
}
```
