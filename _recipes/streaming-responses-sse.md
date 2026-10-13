---
layout: recipe
title: "Streaming Real-Time Events with SSE"
description: "Implement resilient Server-Sent Events (SSE) with automatic backoff reconnection and message deduplication."
category: "Cookbook Recipes"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["Realtime", "SSE", "Streaming", "WebSockets"]
---

# Streaming Real-Time Events with SSE

Implement resilient Server-Sent Events (SSE) with automatic backoff reconnection and message deduplication.

## Problem Statement

When delivering progressive AI tokens, live telemetry metrics, or real-time status feeds, WebSockets often introduce unnecessary complexity (firewall blocks, stateful sockets, separate handshake). Server-Sent Events (SSE) operate over standard HTTP, leverage existing proxies, and support built-in reconnection.



## Implementation

Select your language to view the production-ready server implementation:

```typescript
import express, { Request, Response } from 'express';

const app = express();

app.get('/events', (req: Request, res: Response) => {
  // Set required SSE streaming headers
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache, no-transform');
  res.setHeader('Connection', 'keep-alive');
  res.setHeader('X-Accel-Buffering', 'no'); // Disable NGINX buffering

  res.flushHeaders();

  let counter = 0;
  const interval = setInterval(() => {
    counter++;
    const payload = JSON.stringify({ count: counter, timestamp: Date.now() });
    res.write(`id: ${counter}\ndata: ${payload}\n\n`);

    if (counter >= 10) {
      clearInterval(interval);
      res.write('event: complete\ndata: finished\n\n');
      res.end();
    }
  }, 500);

  req.on('close', () => {
    clearInterval(interval);
    res.end();
  });
});

app.listen(8080);
```
