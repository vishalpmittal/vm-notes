# Netflix's Live Streaming Architecture

How Netflix delivers live events to 100M+ concurrent viewers — peaking at 65M concurrent streams during the Tyson vs Paul fight (2024). Custom origin, dual-redundant encoding, intelligent rate limiting, and >90% metadata cache hit rates.

## Key Takeaways

- **Live Origin** is Netflix's custom server between cloud encoding pipelines and the Open Connect CDN — a quality-control checkpoint that decides which segments get delivered to viewers
- **Dual independent encoding pipelines** in different cloud regions feed Live Origin; an intelligent selector picks the first valid segment from either, providing redundancy without dual-cost downstream
- **Storage evolution**: migrated from S3 to Cassandra-based storage — median latency dropped from **113ms → 25ms**. EVCache (Memcached) added as write-through cache layer
- **Tyson vs Paul (2024)** peaked at **65M concurrent streams**. Survived via priority-based rate limiting that protects live-edge over DVR, plus separated publishing and CDN traffic stacks
- **>90% metadata cache hit ratio during 404 storms** — hierarchical metadata structure enables preemptive request rejection at cache layer, never hitting origin

## The Live Streaming Stack

```
Cloud encoding pipelines (dual, in different regions)
                ↓
        Live Origin server          ← QC checkpoint, picks best segment
                ↓
        Open Connect CDN            ← Netflix's own CDN
                ↓
              Viewers (100M+)
```

## Live Origin — The Quality Control Checkpoint

The custom server between encoders and CDN. Its job:

- Receive segments from multiple encoding pipelines
- Inspect each segment for defects (lightweight media inspection)
- Select the first valid segment to publish
- Reject bad segments before they reach viewers

### Dual Independent Encoding Pipelines

Two complete encoding pipelines run in parallel, in different cloud regions:

- Each produces every segment independently
- The "intelligent selector" in Live Origin picks the first valid one
- If pipeline A's segment is corrupt or late, pipeline B's segment is used

**Why dual pipelines:** redundancy at the source. Encoding pipelines can fail mid-stream; without redundancy, a glitch in one pipeline = a glitch for every viewer.

**Why not dual everything:** the CDN layer doesn't need to be doubled — once Live Origin picks the good segment, one copy serves all viewers via the CDN.

### Manifest Strategy: Fixed 2-Second Segments

- All segments are exactly 2 seconds
- Predictable timing enables request validation (a request for segment N at time T is valid iff `N ≈ T/2`)
- Predictable publishing cadence simplifies cache TTLs

## Storage Evolution: S3 → Cassandra + EVCache

Originally Live Origin used S3 for segment storage. Performance moved them off:

| Metric | S3 | Cassandra-based |
|---|---|---|
| Median latency | 113ms | **25ms** (4.5× faster) |

Then added **EVCache** (Netflix's Memcached fork) as a **write-through caching layer** — handles massive read surges during peak.

Why write-through specifically: on a publish, write to cache AND storage simultaneously. Reads always find the segment in cache (best-case latency). See [caching-strategies.md § write-through](caching-strategies.md#3-write-through).

## Surviving Peak Load: Tyson vs Paul (2024)

**65 million concurrent streams.** For context, this was unprecedented for any live streaming platform.

### Separated Stacks for Publishing vs CDN Traffic

Live Origin's writes (encoders publishing segments) and reads (CDN nodes fetching segments) run on **distinct EC2 stacks**. So:
- A traffic surge on reads doesn't slow down writes
- An issue with encoder publishing doesn't degrade viewer experience
- Each side can scale independently

### Priority-Based Rate Limiting

When the system is congested, not all requests are equal:

| Priority | What | Why |
|---|---|---|
| **Critical** | Origin writes (encoders publishing segments) | If writes stop, no new content for anyone |
| **High** | Live-edge reads (segments being watched right now) | Majority of viewers |
| **Lower** | DVR-mode reads (rewinding, catching up) | Single-user impact only |

Under congestion, DVR requests get rate-limited first. Live viewers stay fed.

This is **prioritized degradation** — the system stays functional for the highest-value traffic by shedding the lowest-value first.

### Traffic Storm Protection

Two specific patterns matter:

**1. Metadata caching with >90% hit ratio during 404 storms**

A "404 storm" happens when many clients request a not-yet-published segment simultaneously (e.g., 30 seconds before it's published, viewers' clocks are wrong, etc.). Without protection, those requests hit Cassandra and crush it.

Netflix caches the 404 result and serves it from cache. >90% of the storm hits the cache, never reaching the origin.

**2. Hierarchical metadata structure**

Metadata is organized as: **event → rendition → segment**. So a request for a non-existent segment can be rejected at the event level (if the event doesn't exist) or rendition level (if the rendition doesn't exist) without ever needing to look up the segment.

Preemptive rejection at higher levels saves work.

**3. TTL-based caching dampens spikes**

CDN nodes cache requests for a TTL. Identical requests within the TTL window get served from cache. This dampens "everyone in the timezone refreshed at the top of the hour" spikes.

## The Generalizable Patterns

### 1. Quality Control Checkpoint Pattern
Don't trust upstream sources blindly. Put a validation/selection layer between producers and consumers. Live Origin's "first valid segment wins" is one instance; this generalizes to any pipeline with multiple upstreams.

### 2. Dual-Active Where Failure Is Visible, Single-Track After
Encoding (where failures show as glitches) is dual-active. The CDN (where dual-active would double cost without proportional benefit) is single. Right-sizing redundancy by where failures hurt.

### 3. Tiered Storage with Aggressive Caching
S3 → Cassandra → EVCache. Each layer faster than the last, with higher hit rates at the upper layers. Get most reads to never reach the cold storage.

### 4. Prioritized Degradation Over Equal-Slow
When you can't serve all requests, serve the high-value ones at full quality and shed the low-value ones. Live-edge over DVR. Real users over bots. Critical writes over reads.

### 5. Cache the Misses, Not Just the Hits
A 404-storm cache turns a DDoS into a non-event. The 404 is also a valid cacheable response.

## Comparison: Netflix vs Twitch vs YouTube Live

(Brief, since the article focuses on Netflix specifically.)

| | Netflix Live | Twitch | YouTube Live |
|---|---|---|---|
| Encoding | Cloud, dual-pipeline | Cloud, transcoder per stream | Cloud, GCP encoder |
| CDN | Open Connect (Netflix-owned) | Twitch CDN + edge | YouTube CDN |
| Use case | Premium events (Tyson, Squid Game Live) | Streamer chat-app | Broad live + on-demand |
| Scale benchmark | 65M concurrent (Tyson) | ~10M concurrent (Worlds) | 100M+ (LiveAid YouTube) |

Architecture differs by use case — Netflix optimizes for premium events with predictable timing; Twitch for thousands of simultaneous streamers; YouTube for the broadest mix.

## Related

- [CDN](cdn.md) — Open Connect is Netflix's CDN
- [Caching strategies § write-through](caching-strategies.md#3-write-through) — EVCache's pattern
- [Distributed system failure modes § cascading failures](distributed-system-failure-modes.md#11-cascading-failures) — what priority rate limiting prevents
- [Rate limiting](rate-limiting.md) — the underlying pattern; Netflix's variant is priority-based
- [Scaling fundamentals](scaling-fundamentals.md) — separated stacks for different workloads

---

**Source:** https://blog.bytebytego.com/p/how-netflix-live-streams-to-100-million
**Date:** 2026-06-05
**Tags:** netflix, live-streaming, video, cdn, open-connect, dual-encoding, evcache, cassandra, priority-rate-limiting, 404-storm, case-study
