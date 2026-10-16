---
layout: guide
title: "Unified MPLS: One LSP Across Many IGP Domains"
description: "How Unified (Seamless) MPLS uses BGP labeled unicast to stitch separate IGP/LDP domains into one end-to-end LSP, and the fast-convergence tools that make it production-ready."
category: "Networking"
difficulty: "Advanced"
read_time: "7 min"
last_updated: "October 2026"
tags: ["Networking", "MPLS", "BGP", "BGP-LU", "Service Provider", "Fast Convergence"]
---

# Unified MPLS: One LSP Across Many IGP Domains

Classic MPLS works beautifully until the network gets big. Put tens of thousands of access and aggregation routers into one IGP and one LDP domain, and you get huge routing tables, slow SPF runs, and a blast radius that covers the whole network every time a link flaps. Split the network into separate domains instead, and you lose the end-to-end LSP your services depend on.

**Unified MPLS** (Cisco's name; the IETF draft [draft-ietf-mpls-seamless-mpls](https://datatracker.ietf.org/doc/draft-ietf-mpls-seamless-mpls/) describes the same idea as *Seamless MPLS*) solves both problems. Each domain keeps its own small IGP and LDP, and BGP carries labels for the router loopbacks between domains. The result is one end-to-end LSP across many small, stable domains.

This guide is based on Cisco's [Unified MPLS Functionality, Features, and Configuration Example](https://www.cisco.com/c/en/us/support/docs/multiprotocol-label-switching-mpls/mpls/118846-config-mpls-00.html). It walks through the idea, the label stack, and the features that make it converge fast.

## Prerequisites

- Comfortable with basic MPLS: labels, LDP, LSPs, and penultimate hop popping. If not, start with [MPLS Explained]({{ '/docs/mpls-explained/' | relative_url }}).
- Familiar with iBGP, route reflectors, and next-hop behavior.
- Helpful: [IS-IS vs OSPF]({{ '/docs/isis-vs-ospf/' | relative_url }}), since each domain runs one of them.

## The Problem: Scale vs. End-to-End

A typical service provider network (for example, a mobile backhaul) has three layers:

| Layer | Typical size | Example devices |
|---|---|---|
| **Access** | Thousands to hundreds of thousands of nodes | Cell site gateways (CSGs) |
| **Aggregation** | Hundreds to thousands | Pre-aggregation routers |
| **Core** | Tens to hundreds | Core routers, ABRs, mobile packet gateways |

Services such as L3VPNs, VPWS, and VPLS need an LSP from one edge router (PE) to another, often across all three layers. You have two traditional options, and both hurt:

- **One giant IGP/LDP domain.** Every router learns every loopback. IGPs aren't built to carry hundreds of thousands of prefixes, and a single flap triggers recalculation everywhere.
- **Separate domains, stitched per service.** Each service needs pseudowire stitching or inter-AS tricks at every boundary, which means more configuration points and more things to break.

## The Idea: Let BGP Carry Labels Between Domains

Unified MPLS splits the job in two:

- **Inside each domain (intra-domain):** a normal IGP plus LDP, carrying only that domain's own loopbacks and infrastructure links.
- **Between domains (inter-domain):** iBGP with **labeled unicast** (BGP-LU) advertises PE loopbacks *with an MPLS label attached*. This was originally specified in RFC 3107, which RFC 8277 has since replaced.

BGP was designed to carry very large numbers of routes, so moving PE loopbacks from the IGP into BGP keeps every IGP small while still giving every PE a labeled path to every other PE.

### What the Border Routers Do

The routers that join two domains are called **ABRs** in Cisco's design. On their BGP-LU sessions they're configured as:

- **Inline route reflectors.** A full mesh of iBGP sessions across thousands of routers isn't practical. Making the ABRs route reflectors means each router only peers within its own segment.
- **Next-hop-self.** When an ABR reflects a route, it sets itself as the BGP next hop. Routers in one domain then only need to reach their local ABR, never a loopback in some far-off domain. That's what keeps each IGP small.

Because the ABR changes the next hop, it also allocates a new BGP label. BGP labels are only swapped where the next hop changes, which is at the ABRs.

## Following One Packet Through the Label Stack

Cisco's document traces an L3VPN prefix `A` advertised by PE31 and reached from PE11, across three domains joined by two ABRs (P1 and P2). Each packet carries up to three labels:

| Label | Purpose | Example value |
|---|---|---|
| **Top: LDP label** | Gets the packet across the current domain to the next ABR | 200, then 201, then 110 |
| **Middle: BGP-LU label** | Identifies the remote PE loopback across domains | 100, then 101 |
| **Bottom: VPN service label** | Tells PE31 which VRF the packet belongs to | 30 (never changes) |

Step by step:

1. **PE11** pushes three labels: VPN label 30, BGP label 100 (learned from P1 for PE31's loopback), and LDP label 200 to reach P1.
2. **Inside domain 1**, the LDP label is swapped hop by hop. The router before P1 pops it (PHP), so P1 receives two labels.
3. **P1 (ABR)** swaps BGP label 100 for 101, the label P2 advertised, and pushes a new LDP label (201) to reach P2.
4. **Inside domain 2**, the same LDP swapping and PHP happens, so P2 receives two labels.
5. **P2 (ABR)** reaches PE31 directly through its own IGP. PE31 advertised an implicit-null BGP label, so P2 **pops** the BGP label and pushes LDP label 110.
6. **PE31** receives the packet with only VPN label 30 after the final PHP, looks up the VRF, and forwards plain IP to the customer.

The service label stays untouched end to end. Only the transport labels change at domain boundaries.

## Making It Converge Fast

Splitting the network into domains keeps things stable, but a provider also needs sub-second recovery. The Cisco design adds four tools.

### BGP Prefix-Independent Convergence (BGP PIC)

Normally, when a BGP next hop fails, the router recomputes the best path for every affected prefix and re-advertises them, which can take seconds with large tables. **BGP PIC** precomputes a backup path and installs it in the forwarding table ahead of time. On failure the router rewrites a single shared next-hop entry, so recovery time no longer depends on how many prefixes are affected. Cisco cites that rewrite taking about 50 ms (most failures under 100 ms) plus IGP convergence, while BGP itself catches up in the background.

### BGP Add-Path

A route reflector normally advertises only its single best path. That leaves clients with nothing to precompute a backup from. **Add-Path** (RFC 7911) lets a BGP speaker advertise multiple paths for the same prefix, which gives BGP PIC the alternate paths it needs.

### Loop-Free Alternates (LFA)

Inside each domain, **LFA** (RFC 5286) lets OSPF or IS-IS precompute a backup next hop that won't loop traffic back. When a link or neighbor fails, the router switches to it immediately. Where the topology offers an LFA, you get roughly 50 ms protection without running RSVP-TE fast reroute.

### Remote LFA (rLFA)

Plain LFA can't protect every topology. Rings are the classic gap. **Remote LFA** (RFC 7490) finds a node more than one hop away that can still reach the destination. It builds a targeted LDP session to that node and tunnels traffic to it with an extra label when the primary path fails. No manual tunnel configuration is needed.

## Configuration Highlights

The Cisco example uses IS-IS Level 2 in the core, IS-IS Level 1 in aggregation, and OSPF in the access network, with IOS XR on the core ABRs. The key BGP-LU pieces of the IOS XR configuration look like this (simplified from the original, which spreads them across the ABR and gateway configs):

```text
router bgp 100
 address-family ipv4 unicast
  allocate-label all              ! attach labels to advertised IPv4 routes
 !
 neighbor-group agg
  address-family ipv4 labeled-unicast
   route-reflector-client
   next-hop-self
```

A few design details from the example are worth copying:

- **Use communities to control what leaks where.** The mobile packet core loopbacks are tagged with one community and the aggregation loopbacks with another. Egress policies only pass the communities each segment actually needs.
- **Don't leak IGP levels.** The core ABR blocks IS-IS Level 1 into Level 2 redistribution. Reachability between domains comes from BGP-LU, not the IGP.
- **Extend into access selectively.** Pre-aggregation routers redistribute only the needed labeled BGP prefixes (filtered by community) into the access OSPF, so cell site routers stay tiny.

## Gotchas

- **One more label.** BGP-LU adds a label to every packet. Combined with the LDP and service labels, plan core MTU for at least three labels (12 extra bytes).
- **Next-hop-self is not optional.** Forget it on one ABR session and routers receive BGP next hops in a far-side domain they can't resolve. Those routes become unusable (or force far-side loopbacks into the local IGP), which defeats the design.
- **Route reflection hides paths.** Without Add-Path, a client behind a route reflector usually only sees one path, so BGP PIC has no backup to precompute.
- **Redistribution loops.** Mutual redistribution between BGP and the access IGP needs tags or communities, or routes can loop between protocols.

## When You Don't Need It

If the whole network comfortably fits in one IGP area with a few hundred routers, plain LDP or Segment Routing in a single domain is simpler. Unified MPLS earns its complexity when the node count, mostly in access, grows into the thousands and beyond.

## Next Steps

- Review the basics in [MPLS Explained]({{ '/docs/mpls-explained/' | relative_url }}).
- Read Cisco's original [Unified MPLS configuration example](https://www.cisco.com/c/en/us/support/docs/multiprotocol-label-switching-mpls/mpls/118846-config-mpls-00.html) for the full configs and verification output.
- Look into Segment Routing, which can replace LDP inside each domain and works with the same BGP-LU design.
