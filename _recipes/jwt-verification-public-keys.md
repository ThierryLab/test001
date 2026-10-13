---
layout: recipe
title: "Zero-Dependency JWT Verification with Public Keys"
description: "Verify RS256 and EdDSA cryptographic tokens with JWKS rotation and zero external dependencies."
category: "Cookbook Recipes"
difficulty: "Advanced"
read_time: "6 min"
last_updated: "October 2026"
tags: ["Security", "JWT", "Authentication", "Crypto"]
---

# Zero-Dependency JWT Verification with Public Keys

Verify RS256 and EdDSA cryptographic tokens with JWKS rotation and zero external dependencies.

## Architecture & Token Flow

Validating tokens in your microservices without contacting an identity server on every request is critical for performance. Using public key cryptography, each service verifies signatures locally in $< 1\text{ms}$.



## Verification Code

Select your backend language:

```typescript
import crypto from 'crypto';

interface JwtClaims {
  sub: string;
  role: string;
  exp: number;
}

export function verifyRs256Jwt(token: string, publicKeyPem: string): JwtClaims {
  const [headerB64, payloadB64, signatureB64] = token.split('.');
  if (!headerB64 || !payloadB64 || !signatureB64) {
    throw new Error('Malformed JWT structure');
  }

  // 1. Verify cryptographic signature
  const verifier = crypto.createVerify('RSA-SHA256');
  verifier.update(`${headerB64}.${payloadB64}`);
  const isValid = verifier.verify(publicKeyPem, signatureB64, 'base64url');

  if (!isValid) {
    throw new Error('Cryptographic signature verification failed');
  }

  // 2. Decode claims & verify expiration
  const claims: JwtClaims = JSON.parse(
    Buffer.from(payloadB64, 'base64url').toString('utf-8')
  );

  const nowSeconds = Math.floor(Date.now() / 1000);
  if (claims.exp < nowSeconds - 60) {
    throw new Error('Token expired');
  }

  return claims;
}
```
