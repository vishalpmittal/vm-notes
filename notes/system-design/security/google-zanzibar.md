# Google Zanzibar: Authorization at Trillions of Records

How Google built a single authorization system handling 10M+ permission checks/sec across Drive, YouTube, Photos, Calendar, and Maps — using relation tuples, Spanner-backed consistency, and a clever consistency token called "zookies."

## Key Takeaways

- **One system, 30+ regions, two trillion permission records, 10M+ checks/sec** — Zanzibar serves every Google product needing fine-grained authorization
- **Relation tuples** (`object#relation@user`) replace ACLs. Tuples can reference other tuples, so "members of group X" works without copying every member into every doc's ACL
- **"Zookies"** — opaque consistency tokens that solve the "new enemy" problem (revoked-user-still-has-access due to stale reads). The client passes the zookie with permission checks to demand at-least-this-fresh data
- **Performance through clever caching**: distributed cache with consistent hashing, request deduplication via lock tables, request hedging (send redundant requests for tail latency), and timestamp quantization for cache sharing
- **Performance numbers**: median 3ms (stale reads), p95 ~60ms (fresh reads), 99.999% availability with per-client resource isolation

## What Zanzibar Replaces

Most apps start with ACLs in their main DB — a table mapping (user, resource, permission). That works until:

- Every Google product has its own permissions system, duplicating effort
- Inheritance gets complex: "members of group G can edit doc D" requires denormalizing every group member into D's ACL
- Cross-product sharing (a Drive doc shared with a YouTube channel's owners) requires understanding two ACL schemas
- Scale: trillions of (user, resource, perm) tuples
- Consistency: an admin revokes access, but a request milliseconds later still sees the old permissions

Zanzibar centralizes all of this.

## Core Data Model: Relation Tuples

Every permission is a tuple of the form:

```
object # relation @ user
```

Examples:
```
doc:readme.md#owner@alice                         (Alice owns this doc)
doc:readme.md#viewer@bob                          (Bob can view this doc)
doc:readme.md#viewer@group:engineering#member     (Anyone in engineering group can view)
group:engineering#member@charlie                  (Charlie is in engineering group)
```

### The Power: Tuples Reference Other Tuples

The third line above is the key innovation. Instead of:
- Adding Charlie to readme.md's viewer list
- Adding every other engineer to readme.md's viewer list
- Re-doing this every time engineering's membership changes

You just say "viewers = engineering group members" and link to the group's membership tuple. Membership changes propagate automatically.

This makes Zanzibar effectively **ReBAC** (Relationship-Based Access Control) — permissions are graphs of relationships, traversed at check time.

## Consistency: The "New Enemy" Problem

The hard problem in distributed authorization: **revoked permissions appearing to still grant access** due to stale reads.

Scenario:
1. Alice has access to doc D
2. Admin revokes Alice's access at time T
3. Alice's request at time T+1ms reads from a replica that's 50ms stale
4. The replica still says Alice has access
5. Bug: revoked user still sees the doc

### Solution: Zookies

A **zookie** is an opaque consistency token encoding a timestamp.

When the application reads a document, it gets back a zookie representing "as of when was this document state read." When checking permissions for that document, the application passes the zookie to Zanzibar.

Zanzibar guarantees: the permission check sees state at least as fresh as the zookie's timestamp. So:
1. App reads document state → gets zookie Z (timestamp T1)
2. App checks "can Alice view this doc?" with zookie Z
3. Zanzibar returns answer based on state as-of T1 or later
4. If Alice was revoked before T1, the answer is no

Built on **Google Spanner** for causally-ordered, timestamped writes — Zanzibar inherits Spanner's external consistency.

## Architecture

```
                                Client
                                  ↓
                          Cluster Coordinator (nearest)
                                  ↓
              ┌─── parallel recursive evaluation ───┐
              ↓                                       ↓
        Cache (consistent-hash)         Spanner (source of truth)
              ↓
        Lock table (dedup concurrent identical requests)
              ↓
        Leopard (precomputed group membership index)
```

### Distributed Cluster Coordinator

Requests route to the nearest cluster. For complex permissions involving group membership traversal, the coordinator recursively contacts other servers to evaluate rules in parallel.

### Spanner as Source of Truth

Spanner provides:
- Globally consistent timestamps (TrueTime)
- Causal ordering across regions
- Strong consistency for tuple reads/writes

Zookies are essentially Spanner timestamps repackaged for client passthrough.

## Performance Optimizations

### Distributed Cache with Consistent Hashing
Permission checks hit the cache layer first. Consistent hashing routes the same query to the same cache node to maximize hit rates.

### Request Deduplication (Lock Tables)
When many concurrent requests for the same permission arrive, only one hits the backend; the others wait for the result. Prevents thundering-herd on cache miss.

### Request Hedging
For low tail latency, send a redundant request to a second replica after a short timeout. Take the first response. Trades extra load for predictable p99.

### Leopard — Precomputed Transitive Group Membership
"Member of group G" can require traversing nested groups (G contains H contains I contains user). Leopard pre-computes the flattened membership for fast nested-group resolution at check time.

### Timestamp Quantization
Round zookie timestamps to coarse boundaries (1-10 seconds). Many requests round to the same timestamp → same cache key → cache hit rate increases dramatically. Trade: slightly more staleness for much better cache utilization.

## Performance Numbers

| Operation | Latency |
|---|---|
| 99% of checks (stale reads) | **median 3ms** |
| Fresh reads (95th percentile) | **~60ms** |
| Writes (0.25% of operations) | **median 127ms** |
| Availability | **99.999%** |

The split matters: 99% of permission checks tolerate stale reads (and are fast); only the 1% needing fresh reads pay the consistency latency.

## Why This Is Influential

Zanzibar inspired a wave of open-source ReBAC systems:
- **OpenFGA** (open-source, Auth0-led, now Cloud Native)
- **SpiceDB** (Authzed)
- **Permify**
- **Warrant**

If you're building fine-grained authorization at significant scale, these systems are the canonical choice. For small apps, plain RBAC in your DB is still fine.

## When You Need Zanzibar-Style ReBAC

Strong fit:
- Multi-tenant SaaS with sharing within and across tenants
- Document collaboration (Drive, Notion, Figma — natural fit)
- Cross-product permissions (Google's actual use case)
- Group-based permissions with nested groups
- Scale where ACL denormalization stops working (millions+ of resources)

Bad fit:
- Single-app, simple roles (RBAC in your DB is sufficient)
- Read-mostly with infrequent permission changes (cache the ACL)
- Latency-critical paths where 3ms is too much (rare)

## Related

- [SSO](sso.md) — authentication (who you are); Zanzibar is authorization (what you can do). Often paired
- [OAuth](oauth.md) — adjacent — OAuth is about delegated authorization; Zanzibar is about fine-grained internal authorization
- [Agent identity and auth](../../ai-ml-ds/concepts/agent-identity-and-auth.md) — agent identity propagation feeds *into* authorization decisions like Zanzibar's
- [Replication and sharding](../database/replication-and-sharding.md) — Spanner is the substrate; Zanzibar inherits its consistency properties
- [Distributed transactions](../distributed-transactions.md) — Spanner uses 2PC + Paxos for consistency at scale
- [CAP](../cap.md) — Zanzibar prioritizes consistency for fresh reads, accepts staleness for performance on stale reads — a per-request tunable

---

**Source:** https://blog.bytebytego.com/p/how-google-manages-trillions-of-authorizations
**Date:** 2026-06-05
**Tags:** google, zanzibar, authorization, rebac, spanner, zookies, leopard, scale, case-study, consistent-hashing, request-hedging
