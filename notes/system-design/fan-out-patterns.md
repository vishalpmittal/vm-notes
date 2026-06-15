# Fan-Out Patterns

How to deliver a single event to many recipients efficiently — the choice between push (fan-out on write), pull (fan-out on read), and hybrid. Used everywhere from social-media timelines to notification systems.

## Key Takeaways

- **Fan-out on write (push)** — at write time, materialize the event into each recipient's inbox. Fast reads, but **hot keys** (celebrities) overwhelm the writer
- **Fan-out on read (pull)** — at read time, gather events from the sources. No hot key problem, no waste for inactive users, but **reads are slower**
- **Hybrid** — push for active users, pull for celebrities and inactive users — what every large social network does in production
- Push is the default at *small scale*; hybrid is mandatory at *large scale* because the cost of an unbounded push (Lady Gaga writes one post → 100M inbox writes) is unacceptable
- The decision per user depends on **two cardinalities**: how many followers (writer fan-out) and how often they read (reader cost amortization)

## The Three Patterns

### Fan-out on Write (Push Model)

When the writer publishes, the system **eagerly fans out** the event into every recipient's inbox/timeline.

- ✅ Reads are O(1) — recipient just reads their own inbox
- ❌ **Hot-key problem**: celebrity writes (millions of followers) cause write storms
- ❌ Waste for inactive followers (you write to inboxes that will never be read)
- Used for: standard social timelines, notifications, broadcasts to small audiences

### Fan-out on Read (Pull Model)

When the recipient reads, the system **gathers events on demand** from the sources they follow.

- ✅ No hot-key problem — Lady Gaga's one write is one write
- ✅ No wasted work for inactive users
- ❌ Reads are slower — query many source feeds, merge, rank
- Used for: low-volume feeds, celebrity-heavy follower graphs, inactive-user reads

### Hybrid

The real-world answer at scale. Decide per-writer (or per-reader):

| Recipient type | Strategy |
|---|---|
| Active user, followee has few followers | **Push** to inbox at write time |
| Active user, followee is a celebrity | **Pull** at read time |
| Inactive user | **Pull** at read time (don't waste push storage) |

The classic example: **Twitter's home timeline** uses push for most followers but pull for high-fan-out accounts (~> 10K followers), then merges the two at read time.

## When to Choose Which

- **Small fanout (~hundreds), most users active** → pure push
- **Huge fanout (millions of followers) at the top of the long tail** → must hybrid or pure pull for those accounts
- **Read-heavy, latency-sensitive** → bias push
- **Write-heavy, read-rare** → bias pull
- **Mixed and you don't want to choose** → hybrid (more code, but matches reality)

## See Also

- [sync-vs-async-communication.md](sync-vs-async-communication.md) — push/pull at the inter-service messaging layer
- [event-driven.md](event-driven.md) — the messaging substrate that makes fan-out reliable
- [caching-strategies.md](caching-strategies.md) — hot-key problems show up here too

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** fan-out, push-pull, social-feed, timelines, hot-key, hybrid-architecture, twitter-timeline
