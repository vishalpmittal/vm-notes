# AWS S3 System Design

How S3 achieves 99.999999999% (eleven nines) durability through metadata/data separation, object immutability, and massive horizontal scaling. The substrate underneath most cloud-native data architectures.

> Partial paywall — the deeper durability and partitioning mechanisms are gated. This note covers the publicly-accessible portion.

## Key Takeaways

- **Eleven nines durability** (99.999999999%) — storing 10M objects for 10,000 years would lose ~1 object on average
- **Metadata and data are separated** — a metadata store maps human-readable names → UUIDs (~1KB per record); a data store holds raw bytes addressed only by UUID. Each layer scales and optimizes independently
- **Objects are immutable** — you can replace the whole thing or version it, but you cannot edit part of an object. This constraint enables the durability + scale properties
- Request flow runs through IAM verification on both upload and download — every operation is authenticated and authorized before touching storage
- S3 is the **foundation layer** for cloud-native data architectures — data warehouses (Snowflake, BigQuery), data lakes, and even other databases store their files on S3

## Architecture: Metadata/Data Separation

```
   Client
     │
     ▼
   Load Balancer
     │
     ▼
   API Service
     │
     ├─→ IAM (authn/authz)
     │
     ├─→ Metadata Store        ← name, bucket ID, UUID, timestamps, tags (~1KB/record)
     │
     └─→ Data Store            ← raw bytes addressed only by UUID
```

The split matters because **the two stores have different characteristics**:

| | Metadata Store | Data Store |
|---|---|---|
| **Record size** | ~1KB (object name, bucket, UUID, tags, timestamps) | KB to TB (the actual object) |
| **Access pattern** | High-rate lookups (name → UUID) | Streaming reads/writes of bytes |
| **Optimization** | Low-latency key-value lookups | Throughput + durability |
| **Scaling** | Sharded by bucket / partition | Massive parallelism, erasure coding |

By separating them, S3 can scale each independently — metadata lookups don't compete with byte-streaming I/O.

## Object Immutability

> "You cannot edit part of an object. You replace the whole thing, or you version it."

This is the single most consequential design constraint. Implications:

- **Simpler concurrency** — no in-place mutations means no read/write conflicts on object bytes
- **Easier durability guarantees** — once written, the bytes never change, so replication and erasure coding are simpler
- **Versioning is cheap** — every "edit" is just a new object with a new version ID
- **No partial-write failures to recover from** — write either succeeded fully or didn't

The trade-off: workloads that *do* need in-place mutation (databases, transaction logs) can't use S3 directly. They use block storage (EBS) or local disk for the mutable part, and write durable snapshots to S3.

## Request Flow Details

### Upload (PUT)

```
1. Client → Load Balancer
2. Load Balancer → API Service
3. API Service → IAM        (verify caller can write to this bucket)
4. API Service → Data Store (write bytes, get back UUID)
5. API Service → Metadata Store (record name → UUID mapping)
6. API Service → Client (200 + ETag)
```

The order matters: bytes are written first, metadata second. If metadata recording fails, the bytes still exist (addressable by UUID) but aren't reachable by name — a stale fragment that gets garbage-collected eventually.

### Download (GET)

```
1. Client → API Service
2. API Service → IAM        (verify caller can read this object)
3. API Service → Metadata Store (look up name → UUID)
4. API Service → Data Store (stream bytes by UUID)
5. Data Store → Client (or via API Service)
```

## Durability: How Eleven Nines Works

The article paywalls the details, but the standard S3 approach (publicly documented elsewhere):

- **Erasure coding** — each object is split into N data chunks + K parity chunks across multiple devices and availability zones
- **Reed-Solomon or similar codes** — any N of the N+K chunks can reconstruct the object
- **Cross-AZ replication** — chunks distributed across 3+ availability zones; a single AZ failure doesn't lose data
- **Continuous integrity checking** — chunks are periodically re-checksummed; corruption triggers re-replication from healthy copies

The math: if a single drive has 99.99% durability per year, distributing across enough drives with proper coding gets you to eleven nines.

## Storage Classes (Mentioned But Not Detailed)

S3 has tiered storage:

- **S3 Standard** — eleven nines, high throughput, milliseconds latency
- **S3 Standard-IA** (Infrequent Access) — same durability, cheaper, with retrieval fees
- **S3 Glacier** — same durability, much cheaper, minutes-to-hours retrieval
- **S3 Glacier Deep Archive** — cheapest, hours-to-12-hours retrieval

The classes share the architecture; they differ in where the bytes physically live (faster vs slower storage tiers).

## Why S3 Is the Cloud-Native Substrate

S3 is the bottom layer of most modern data architectures:

- **Data warehouses** (Snowflake, BigQuery, Redshift) store table data on S3 (or equivalents like GCS, ABS)
- **Data lakes** are essentially "use S3 as a filesystem with some metadata"
- **Streaming platforms** (Kafka tiered storage) offload old segments to S3
- **Databases** (some serverless ones) store their underlying files on S3

The pattern: any system that needs durable, scalable, cost-optimized storage layered behind a smarter access layer.

See [cloud-native-data-architecture.md](cloud-native-data-architecture.md) for the broader pattern — S3 is the canonical example of "object storage as the foundation."

## Generalizable Lessons

1. **Separate concerns that scale differently** — metadata access patterns ≠ data byte streaming
2. **Constraints enable capabilities** — object immutability is a constraint that *enables* eleven nines durability
3. **Stateless API tier in front of stateful stores** — lets the API scale horizontally while the data layer handles the hard scaling
4. **Authenticate at the edge** — IAM check on every request, not on rare admin operations
5. **Build for failure** — erasure coding + cross-AZ replication assumes individual components fail constantly

## Related

- [Cloud-native data architecture](cloud-native-data-architecture.md) — S3 is the canonical example of object storage as the substrate
- [Replication and sharding](replication-and-sharding.md) — object stores are extreme cases of sharding-by-key
- [CDN](../cdn.md) — S3 is often the origin behind a CDN
- [Distributed system failure modes](../distributed-system-failure-modes.md) — the failure modes S3's design eliminates

---

**Source:** https://newsletter.systemdesign.one/p/aws-s3-system-design
**Date:** 2026-06-05
**Tags:** aws, s3, object-storage, durability, erasure-coding, metadata-separation, immutability, cloud-native, system-design, paywalled
