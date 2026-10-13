---
layout: recipe
title: "HMAC-SHA256 Webhook Signature Verification"
description: "Prevent replay and spoofing attacks by cryptographically verifying inbound webhooks with constant-time equality."
category: "Cookbook Recipes"
difficulty: "Beginner"
read_time: "4 min"
last_updated: "October 2026"
tags: ["Webhooks", "Security", "HMAC", "Best Practices"]
---

# HMAC-SHA256 Webhook Signature Verification

Prevent replay and spoofing attacks by cryptographically verifying inbound webhooks with constant-time equality.

## Why Verification Is Required

Public endpoints accepting webhook alerts must ensure the sender holds your secret, the payload was not tampered with in transit, and attackers are not replaying a captured message.



## Verification Implementation

Multi-language implementations using constant-time comparison:

```typescript
import crypto from 'crypto';

export function verifyWebhookSignature({
  rawBody,
  signatureHeader,
  secret,
  toleranceSeconds = 300,
}: {
  rawBody: string;
  signatureHeader: string;
  secret: string;
  toleranceSeconds?: number;
}): boolean {
  // Format: "t=1728839182,v1=9a2f..."
  const parts = signatureHeader.split(',').reduce<Record<string, string>>((acc, item) => {
    const [k, v] = item.split('=');
    if (k && v) acc[k] = v;
    return acc;
  }, {});

  const timestamp = parseInt(parts['t'] || '0', 10);
  const signature = parts['v1'];
  if (!timestamp || !signature) return false;

  // Check replay window
  const now = Math.floor(Date.now() / 1000);
  if (Math.abs(now - timestamp) > toleranceSeconds) {
    throw new Error('Webhook timestamp outside allowed tolerance');
  }

  // Calculate expected HMAC
  const signedPayload = `${timestamp}.${rawBody}`;
  const expectedSig = crypto
    .createHmac('sha256', secret)
    .update(signedPayload)
    .digest('hex');

  // Constant-time comparison
  return crypto.timingSafeEqual(
    Buffer.from(signature, 'hex'),
    Buffer.from(expectedSig, 'hex')
  );
}
```
