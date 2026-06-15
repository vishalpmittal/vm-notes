# Concurrency Fundamentals

The conceptual difference between **multithreading**, **concurrency**, and **parallelism** — and the four conditions that cause **deadlocks**. Misusing these terms is a common interview tell.

## Key Takeaways

- **Multithreading**, **concurrency**, and **parallelism** are *not* synonyms — they describe different things at different layers
- **Concurrency = dealing with multiple things at once**; **parallelism = doing multiple things at once**. You can have concurrency on a single core (via context switching) but you need multiple cores for parallelism
- A **deadlock** requires all four of Coffman's conditions simultaneously — break any one and you can't deadlock
- The classic deadlock-prevention strategies attack one of the four conditions (lock ordering attacks Circular Wait; lock timeouts attack Hold-and-Wait)
- **Livelock** ≠ deadlock — in livelock, threads are *active* but making no progress (e.g., both keep yielding to each other politely)

## The Three Concepts, Carefully Distinguished

| Concept | What it is | Where it lives |
|---|---|---|
| **Multithreading** | Multiple threads of execution within a single process, sharing memory | Process-level construct |
| **Concurrency** | Logical structure for *dealing with* multiple tasks at once — interleaved execution, possibly on one core via context switching | Application design property |
| **Parallelism** | Physical *execution* of multiple tasks simultaneously, requires multiple cores | Hardware-enabled behavior |

The standard examples:

- **Single-core CPU running 100 threads** → concurrent (via time-slicing), not parallel
- **8-core CPU running 8 threads on 8 cores** → both concurrent *and* parallel
- **Async/await on a single thread** → concurrent, not parallel, not multithreaded
- **MapReduce across 1000 machines** → parallel (across cores) *and* concurrent

> Concurrency is about **structure**; parallelism is about **execution**. (Rob Pike)

## Deadlocks — The Four Coffman Conditions

A deadlock can occur **only when all four hold simultaneously**:

| # | Condition | What it means |
|---|---|---|
| 1 | **Mutual Exclusion** | At least one resource is held in a non-shareable mode (only one process at a time can use it) |
| 2 | **Hold and Wait** | A process holding at least one resource is waiting to acquire additional resources held by other processes |
| 3 | **No Preemption** | A resource can only be released voluntarily by the process holding it — not taken away |
| 4 | **Circular Wait** | A chain of processes exists such that each is waiting for a resource held by the next process in the chain |

### Prevention strategies (each attacks one condition)

| Strategy | Attacks | How |
|---|---|---|
| **Lock ordering** (acquire in global order) | Circular Wait | If everyone acquires locks A→B, you can't get the cycle |
| **Lock timeouts** | Hold-and-Wait | Release everything and retry if you can't get the next lock in time |
| **All-or-nothing acquisition** | Hold-and-Wait | Acquire all needed locks upfront or none |
| **Resource preemption / forced release** | No Preemption | The deadlock detector kills one participant |
| **Lock-free data structures** | Mutual Exclusion | Uses atomic CAS operations instead of locks |

## Related Pitfalls

| Problem | What it is | Example |
|---|---|---|
| **Livelock** | Threads are active and changing state but making no progress | Two threads detect a conflict and both back off — they keep retrying and re-conflicting forever |
| **Starvation** | A thread never gets CPU/lock because higher-priority work always preempts | Low-priority job in a strict-priority scheduler under sustained high-priority load |
| **Priority inversion** | Low-priority thread holds a lock a high-priority thread needs, blocking it indirectly | The classic Mars Pathfinder bug |
| **Race condition** | Correctness depends on the relative timing of two operations | Read-modify-write without atomicity (`counter++` from two threads) |

## See Also

- [../system-design/locking.md](../system-design/locking.md) — pessimistic vs optimistic locking at the application level
- [../system-design/jvm.md](../system-design/jvm.md) — JVM threading + monitor model
- [oop-concepts.md](oop-concepts.md) — OO concurrency primitives

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** concurrency, parallelism, multithreading, deadlock, coffman-conditions, livelock, race-condition, lock-ordering
