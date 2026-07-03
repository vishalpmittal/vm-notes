# Voice AI Production Infrastructure

## Key Takeaways
- OpenAI serves 900 million weekly users with a two-layer WebRTC architecture — stateless relay (geographic edges) + stateful transceiver (session ownership) — avoiding traditional SFU overhead for 1:1 sessions.
- The core innovation is **ICE ufrag routing**: the transceiver embeds routing metadata in the WebRTC ICE username fragment so the relay can forward the first UDP packet to the right backend without a database lookup.
- Three targeted Linux/Go optimizations — `SO_REUSEPORT`, `runtime.LockOSThread`, pre-allocated buffers — deliver the needed performance; kernel-bypass frameworks were evaluated and rejected as unnecessary.
- The relay is "stateless" in design but maintains in-memory flow tables and a Redis fallback for recovery — illustrating the practical gap between stateless ideals and production requirements.
- This architecture is optimized exclusively for 1:1 sessions; multiparty features would require significant rework.

## The Problem: Voice AI on Kubernetes

Voice AI creates a fundamental tension with Kubernetes: sessions are stateful and long-lived (a voice conversation), but Kubernetes is designed for ephemeral, replaceable compute. Every pod reschedule or restart risks dropping a live session.

OpenAI's solution: decouple the stateful and stateless layers so only the stateful component owns session risk.

## Two-Layer Architecture

**Stateless Relay** (geographic edge layer)
- Handles protocol-aware UDP packet routing
- Deploys at multiple geographic ingress points (low-latency proximity to users)
- No session state — forwards packets based on in-memory flow tables
- Cloudflare provides proximity-based steering for signaling

**Stateful Transceiver** (session ownership layer)
- Owns the full WebRTC stack: ICE, DTLS, SRTP, session lifecycle
- Lives inside Kubernetes; sessions are anchored here
- One transceiver owns one session's state for the session's duration

**Why not SFU?** Standard Selective Forwarding Unit architecture is designed for multiparty calls. OpenAI's sessions are overwhelmingly 1:1 (user ↔ model), making SFU overhead unnecessary.

## The Core Innovation: ICE ufrag Routing

The hardest problem in this design: how does the relay know which transceiver to forward the *first* UDP packet to, before any flow table entry exists?

**Traditional approach:** database lookup on first packet — too slow for real-time voice.

**OpenAI's approach:** encode routing metadata directly in the ICE username fragment (ufrag).

WebRTC ICE negotiation happens during signaling (before audio starts). At this stage, the transceiver generates a server-side ICE ufrag that encodes the transceiver's address. When the relay receives the initial STUN binding request, it parses the ufrag to determine the destination, then:
1. Forwards the packet to the correct transceiver
2. Writes a source → destination mapping to its in-memory flow table
3. Subsequent packets hit the flow table directly — no parsing needed

This eliminates database round-trips on hot path packets.

**Limitation:** requires controlling server-side ufrag generation. Third-party WebRTC infrastructure can't be dropped in without modification.

## Performance Optimizations

Three targeted choices in the relay implementation:

| Optimization | What it does |
|---|---|
| `SO_REUSEPORT` | Linux socket option — multiple worker goroutines bind the same UDP port, distributing receive load without a single-process bottleneck |
| `runtime.LockOSThread` | Pins each goroutine to an OS thread — improves CPU cache locality for hot packet-processing loops |
| Pre-allocated buffers | Buffer pools allocated at startup — eliminates per-packet heap allocation and GC pressure |

The team explicitly evaluated kernel-bypass frameworks (DPDK-style) and found ordinary optimizations were sufficient for their workload.

## Global Distribution

- Relay nodes deploy at geographic edges — users connect to the nearest relay
- Cloudflare handles proximity-based DNS/anycast steering for signaling requests to nearby transceiver clusters
- The ufrag encodes sufficient routing information to work across multi-region deployments

## Design Tradeoffs

| Decision | Benefit | Cost |
|---|---|---|
| Split relay/transceiver | Kubernetes restarts only affect transceiver; relay is always up | Two-hop latency; operational complexity |
| ICE ufrag routing | Zero database lookups on first packet | Must control server-side ufrag; non-standard |
| 1:1 optimization | Simpler architecture, lower overhead | Multiparty features require redesign |
| "Stateless" relay | Fast failover; easy horizontal scaling | Still needs in-memory flow tables + Redis |
| Go runtime pinning | Cache locality, lower jitter | Bypasses Go scheduler's load balancing |

## Related
- [real-time-ai-interaction.md](real-time-ai-interaction.md) — model-level architecture for real-time voice AI (micro-turns, two-model coordination, Thinking Machines)
- [inference-engineering.md](inference-engineering.md) — serving optimization, batching, KV cache

---

**Source:** https://blog.bytebytego.com/p/how-openai-delivers-low-latency-voice
**Source:** https://openai.com/index/delivering-low-latency-voice-ai-at-scale/
**Date:** 2026-07-01
**Tags:** webrtc, voice-ai, real-time-ai, openai, relay-transceiver, ice-routing, production-systems, kubernetes, go, latency, infrastructure
