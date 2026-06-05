# Caching Strategies

## Key Takeaways

- Five caching strategies exist; pick based on read/write ratio and consistency tolerance — **cache-aside is the safe default**
- **Cache-aside** (lazy loading) — app reads cache, falls back to DB on miss, then populates cache. Simple, but first reads are slow and cache can drift from DB
- **Write-through** — writes go to cache *and* DB synchronously. Strong read-after-write consistency, at the cost of slower writes and wasted cache space for rarely-read data
- **Write-behind** (write-back) — writes hit cache, async batch flushes to DB. Fastest writes, but data loss if cache crashes before flush
- **Cache stampede** is the failure mode to design for — when a hot key expires, hundreds of requests miss simultaneously and hammer the DB

## The Five Strategies

| # | Strategy | Read path | Write path | Best for | Risk |
|---|---|---|---|---|---|
| 1 | **Cache-Aside** | App checks cache → on miss, app reads DB and populates cache | App writes DB; cache is invalidated or updated separately | Read-heavy, eventually consistent | Cache/DB drift; first miss is slow |
| 2 | **Read-Through** | App reads cache only; cache loads from DB on miss | App writes DB directly | Simplifying the app layer | Cache coupled to data layer |
| 3 | **Write-Through** | App reads cache only | App writes cache, cache writes DB synchronously | Read-after-write consistency | Slower writes; cache holds cold data |
| 4 | **Write-Behind** | App reads cache only | App writes cache; cache flushes to DB asynchronously | Write-heavy, lossy-tolerant | Data loss if cache crashes pre-flush |
| 5 | **Stampede Prevention** | Single regenerator; others wait or get stale | N/A — wraps any strategy above | Popular keys with expiration | Adds locking/coalescing complexity |

## Cache-Aside in Detail

```
read(key):
  v = cache.get(key)
  if v is None:
    v = db.get(key)
    cache.set(key, v, ttl=300)
  return v

write(key, value):
  db.put(key, value)
  cache.delete(key)   # or cache.set(key, value)
```

**Why it's the default:** the app owns both paths, so the cache can fail or be cold without breaking correctness. Trade-off: invalidation is your problem ("there are only two hard things in computer science...").

## Write-Through vs. Write-Behind

**Write-through:**
```
write(key, value):
  cache.set(key, value)   # synchronous
  db.put(key, value)      # synchronous
```
Both succeed or both fail (if you wrap them). Reads after a write always see the new value.

**Write-behind:**
```
write(key, value):
  cache.set(key, value)
  queue.publish(WriteOp(key, value))   # background worker flushes
```
Cache is the source of truth until the flush completes. If the cache loses the entry before the worker drains the queue, the write is lost.

## Cache Stampede Prevention

Symptom: a hot key with `ttl=60s` expires; 10,000 concurrent requests miss the cache, all hit the DB, DB falls over.

Three common mitigations:

| Technique | How it works |
|---|---|
| **Request coalescing** (single-flight) | Only the first miss queries the DB; concurrent misses for the same key wait for the result |
| **Early refresh** (probabilistic) | Refresh the entry before it actually expires (e.g., 80% of TTL elapsed → trigger background refresh) |
| **Locking / mutex** | First requester takes a lock and regenerates; others serve stale or wait |

## Choosing a Strategy

```
Read-heavy + eventual consistency OK → Cache-Aside
Read-heavy + strong consistency needed → Write-Through + Cache-Aside reads
Write-heavy + loss tolerable → Write-Behind
Hot keys with expiration → Stampede Prevention on top of any strategy
```

## Related

- [Redis](database/redis.md) — implementation of the cache itself
- [Distributed system failure modes](distributed-system-failure-modes.md) — dual-write, read-write splitting
- [CAP](cap.md) — consistency tradeoffs in distributed reads

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-06-04
**Tags:** caching, cache-aside, write-through, write-behind, read-through, stampede, redis, system-design
