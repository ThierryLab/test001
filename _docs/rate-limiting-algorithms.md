---
layout: guide
title: "Sliding Window Rate Limiting"
description: "Detailed analysis of distributed rate limiting algorithms, token buckets, and boundary spike mitigation."
category: "Security & Governance"
subcategory: "Access Control"
difficulty: "Intermediate"
read_time: "4 min"
last_updated: "October 2026"
tags: ["RateLimiting", "Security", "Resilience"]
---

# Sliding Window Rate Limiting

Detailed analysis of distributed rate limiting algorithms, token buckets, and boundary spike mitigation.

## Sliding Window Algorithm

Fixed window counters suffer from boundary bursts. The sliding window algorithm weights previous and current minute counts to guarantee smooth traffic distribution across rolling intervals.



## Implementation

Production sliding window limiter with in-memory fallback:

```typescript
export class SlidingWindowLimiter {
  private store = new Map<string, { prev: number; curr: number; start: number }>();

  constructor(private windowMs = 60_000, private max = 100) {}

  isAllowed(key: string): boolean {
    const now = Date.now();
    const windowStart = Math.floor(now / this.windowMs) * this.windowMs;
    let entry = this.store.get(key);

    if (!entry || entry.start < windowStart - this.windowMs) {
      entry = { prev: 0, curr: 0, start: windowStart };
      this.store.set(key, entry);
    } else if (entry.start === windowStart - this.windowMs) {
      entry = { prev: entry.curr, curr: 0, start: windowStart };
      this.store.set(key, entry);
    }

    const elapsed = now - entry.start;
    const weight = (this.windowMs - elapsed) / this.windowMs;
    const estimate = Math.floor(entry.prev * weight) + entry.curr;

    if (estimate >= this.max) return false;
    entry.curr++;
    return true;
  }
}
```
