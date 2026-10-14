---
layout: guide
title: "Request Validation & Payloads"
description: "Strict JSON schema definitions, field constraints, header specifications, and boundary rules."
category: "API Reference"
difficulty: "Intermediate"
read_time: "4 min"
last_updated: "October 2026"
tags: ["Validation", "Schemas", "JSON"]
---

# Request Validation & Payloads

Strict JSON schema definitions, field constraints, header specifications, and boundary rules.

## Schema Definition (OpenAPI 3.1)

All incoming POST and PUT requests are validated against strict JSON schemas with field type enforcement:

```json
{
  "type": "object",
  "required": ["name", "region"],
  "properties": {
    "name": {
      "type": "string",
      "minLength": 3,
      "maxLength": 64
    },
    "region": {
      "type": "string",
      "enum": ["us-west-2", "us-east-1", "eu-west-1"]
    },
    "tags": {
      "type": "array",
      "items": { "type": "string" }
    }
  }
}
```
