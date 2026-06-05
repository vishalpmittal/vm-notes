# Content Delivery Networks (CDN)

## Key Takeaways

- A CDN is a globally distributed cache layer for static content — edge nodes serve users from nearby PoPs instead of the origin
- Three wins: **lower latency** (geographic proximity), **lower origin load** (most requests never reach origin), and **DDoS absorption** (edges fan out attack traffic)
- The hardest CDN problem is **cache invalidation** — propagating "this asset changed" across hundreds of edge nodes takes seconds to minutes
- Modern CDNs do more than static: edge functions, image resizing, A/B routing, WAF, bot detection, even SSR
- Cost model is bandwidth-out from edge — for a global app, CDN bandwidth often dominates the infrastructure bill

## Why CDNs Exist

Without a CDN:
```
user (Sydney) ──── 200ms ──── origin (us-east-1) ──── response
```

With a CDN:
```
user (Sydney) ──── 5ms ──── edge (Sydney PoP) ──── response (cache hit)
                              │
                              └── (cache miss) ──── 200ms ──── origin ──── response
```

Once the asset is cached at the Sydney edge, every subsequent Sydney user pays 5ms instead of 200ms.

## Architecture

```
                    Origin (us-east-1)
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
         PoP us-west  PoP eu-west  PoP ap-south
              │           │           │
         users in     users in     users in
         California   London       Mumbai
```

**PoP** = Point of Presence, a cluster of edge servers in one city/region. Major CDNs run hundreds (Cloudflare ~300+, Fastly ~100+, AWS CloudFront ~600 edge locations).

## What to Cache

| Good fit | Bad fit |
|---|---|
| Images, video, fonts, CSS, JS bundles | User-specific dashboards |
| Public API responses (rate-limited reads) | Authenticated profile data |
| Documentation, marketing pages | Cart state, session data |
| Software downloads, package mirrors | Real-time prices, stock balances |
| Hashed asset filenames (`app.a3f2.js`) | Anything with `Cache-Control: no-store` |

**Cache key includes:** URL, sometimes query string, sometimes headers (Vary). For dynamic content, the cache key matters more than people realize — a careless `Vary: Cookie` blows up your cache hit rate.

## Cache Invalidation

Three strategies, in increasing order of pain:

| Approach | How | Trade-off |
|---|---|---|
| **TTL expiration** | Set `Cache-Control: max-age=86400`; assets auto-expire | Simple; but stale content visible until TTL |
| **Versioned URLs** | `app.v2.js`, `image.a3f2.png` (content hash in filename) | Best for static assets; no invalidation needed since URL changes |
| **Purge API** | Call CDN API to evict specific keys | Slow propagation (seconds to minutes); rate-limited; complex |

**Rule of thumb:** versioned URLs for everything you can; TTL for the rest; purge as a break-glass.

## Cache Hierarchies

CDNs aren't flat. A request flow often passes through layers:

```
user ──> edge PoP ──> regional cache ──> origin shield ──> origin
         (Sydney)     (APAC)             (single point)    (us-east-1)
```

- **Edge PoP** — first stop, highest hit rate for popular content
- **Regional cache** — shared by many PoPs in a region; absorbs traffic for less-popular content
- **Origin shield** — single shared cache between all CDN traffic and your origin; protects origin from request fan-out during cold caches
- **Origin** — your actual backend

A cache miss at the edge falls through the hierarchy. The shield catches the "thundering herd" problem when a popular asset expires globally at the same time.

## Edge Compute

Modern CDNs let you run code at the edge:

- **Cloudflare Workers** — V8 isolates, sub-millisecond cold start
- **Fastly Compute@Edge** — WebAssembly
- **AWS CloudFront Functions / Lambda@Edge** — JS / Node
- **Vercel Edge Functions, Netlify Edge** — built on top of the above

Use cases:
- **A/B testing routing** without origin round trips
- **Authentication checks** (JWT verification) at the edge
- **Image transformation** on-the-fly
- **Personalization** (geolocation-based content, currency conversion)
- **API gateway** functions (rate limiting, request shaping)

Edge functions are stateless and fast but constrained — no long-running connections, tight CPU/memory limits, no native DB access (use Workers KV, D1, Durable Objects).

## CDN as DDoS Defense

Because the CDN absorbs requests at hundreds of PoPs:
- Volumetric attacks fan out across the global edge network
- Origin is shielded — most traffic never reaches it
- CDNs sit in front of WAFs, bot-detection, rate limiting

This is often the *only* reason a small site uses Cloudflare's free tier.

## Cost Reality

Bandwidth out of major CDNs (rough order):
- Cloudflare — flat-rate / free tier for many use cases
- Fastly, CloudFront — per-GB egress, region-dependent
- Origin egress to CDN is often free (AWS S3 → CloudFront, GCS → Cloud CDN)

For a high-traffic site, CDN bandwidth can be 50%+ of infra cost. Optimizations:
- Compression (Brotli > gzip)
- Image formats (AVIF, WebP)
- Cache hit ratio — every 1% improvement is bandwidth saved
- Image resizing at edge (don't serve a 4MP image to a thumbnail)

## Related

- [Forward/reverse proxy and API gateway](forward-reverse-proxy-and-api-gateway.md) — CDN is a reverse proxy at global scale
- [Caching strategies](caching-strategies.md) — application-level caching complements CDN
- [Security/oauth.md](security/oauth.md) — auth checks at the edge

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-06-04
**Tags:** cdn, edge-computing, caching, cloudflare, fastly, cloudfront, ddos, latency, system-design
