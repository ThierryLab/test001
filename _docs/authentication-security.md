---
layout: guide
title: "Authentication & Token Security"
description: "Public-key cryptography, JWT verification, token rotation strategies, and security best practices."
category: "Security & Governance"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["Security", "JWT", "Encryption"]
---

# Authentication & Token Security

Public-key cryptography, JWT verification, token rotation strategies, and security best practices.

## Asymmetric Key Verification

Tokens are signed with RSA-PSS using SHA-256 (PS256) or ECDSA using curve P-256 (ES256). Verify tokens using the published JWKS endpoint.



## Key Verification Implementation

Example JWT verification helper in TypeScript:

```typescript
import { createRemoteJWKSet, jwtVerify } from 'jose';

const JWKS = createRemoteJWKSet(new URL('https://api.docucraft.dev/.well-known/jwks.json'));

export async function authenticateRequest(authHeader?: string) {
  if (!authHeader?.startsWith('Bearer ')) {
    throw new Error('Missing or malformed Authorization header');
  }

  const token = authHeader.substring(7);
  const { payload } = await jwtVerify(token, JWKS, {
    issuer: 'https://auth.docucraft.dev',
    audience: 'https://api.docucraft.dev',
  });

  return payload;
}
```
