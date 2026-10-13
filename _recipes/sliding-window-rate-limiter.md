---
layout: recipe
title: "Sliding Window Rate Limiter with Memory Fallback"
description: "Protect public APIs from traffic spikes using a rolling window counter with atomic increments."
category: "Cookbook Recipes"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["Performance", "RateLimiting", "Resilience", "Redis"]
---

# Sliding Window Rate Limiter with Memory Fallback

Protect public APIs from traffic spikes using a rolling window counter with atomic increments.

## Sliding Window Algorithm Explained

Fixed window counters suffer from boundary bursts: a client can exhaust 100% of their limit at 11:59 and another 100% at 12:00, doubling their intended throughput. Sliding windows weight previous and current intervals to enforce smooth rate bounds.



## Implementation Code

Clean in-memory rate limiter with sliding window estimation:

```typescript
export class SlidingWindowRateLimiter {
  private windows = new Map<string, { prevCount: number; currentCount: number; windowStart: number }>();

  constructor(
    private readonly windowSizeMs: number = 60_000,
    private readonly maxRequests: number = 100
  ) {}

  public isAllowed(key: string): { allowed: boolean; remaining: number; resetMs: number } {
    const now = Date.now();
    const currentWindowStart = Math.floor(now / this.windowSizeMs) * this.windowSizeMs;

    let entry = this.windows.get(key);
    if (!entry || entry.windowStart < currentWindowStart - this.windowSizeMs) {
      entry = { prevCount: 0, currentCount: 0, windowStart: currentWindowStart };
      this.windows.set(key, entry);
    } else if (entry.windowStart === currentWindowStart - this.windowSizeMs) {
      entry = { prevCount: entry.currentCount, currentCount: 0, windowStart: currentWindowStart };
      this.windows.set(key, entry);
    }

    const timeIntoCurrent = now - entry.windowStart;
    const weight = (this.windowSizeMs - timeIntoCurrent) / this.windowSizeMs;
    const estimatedCount = Math.floor(entry.prevCount * weight) + entry.currentCount;

    if (estimatedCount >= this.maxRequests) {
      return {
        allowed: false,
        remaining: 0,
        resetMs: entry.windowStart + this.windowSizeMs - now,
      };
    }

    entry.currentCount++;
    return {
      allowed: true,
      remaining: this.maxRequests - estimatedCount - 1,
      resetMs: entry.windowStart + this.windowSizeMs - now,
    };
  }
}
```
