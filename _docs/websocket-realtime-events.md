---
layout: guide
title: "Real-Time Events & WebSockets"
description: "Bi-directional WebSocket protocol for real-time document change subscription and live cluster metrics."
category: "API Reference"
subcategory: "Streaming Protocols"
difficulty: "Intermediate"
read_time: "5 min"
last_updated: "October 2026"
tags: ["WebSocket", "Streaming", "Protocols"]
---

# Real-Time Events & WebSockets

Bi-directional WebSocket protocol for real-time document change subscription and live cluster metrics.

## Connection Handshake

Establish a secure WebSocket connection passing your authorization bearer token in the query string or initial protocol payload:

```typescript
const socket = new WebSocket('wss://api.docucraft.dev/v1/events?token=sec_live_9f81a7b8e');

socket.addEventListener('open', () => {
  socket.send(JSON.stringify({
    action: 'subscribe',
    channels: ['deployments', 'alerts']
  }));
});

socket.addEventListener('message', (event) => {
  const payload = JSON.parse(event.data);
  console.log('[RealTime Event]', payload);
});
```

## Heartbeat & Reconnection Protocol

The cluster sends `ping` frames every 30 seconds. Clients must respond with `pong` within 5 seconds to prevent keep-alive termination.


