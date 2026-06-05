# Data Processing Patterns

## Key Takeaways

- **MapReduce** is the batch model — split work across machines, combine results. High latency, high accuracy, ideal for end-of-day jobs that scan terabytes
- **Stream processing** handles events one-at-a-time with sub-second latency — required for real-time analytics, fraud detection, recommendations
- **Lambda architecture** runs both in parallel: stream for "good enough now," batch for "correct later" — pays for accuracy with code duplication and two pipelines
- **Kappa architecture** is the modern simplification: stream-only, reprocess from the log when batch-quality results are needed
- The right choice is set by **latency requirement and correctness budget** — batch is cheaper and simpler if you can wait

## MapReduce (Batch Processing)

```
input data (TB scale)
    │
    ▼
SPLIT into chunks → distribute to N workers
    │
    ▼
MAP: each worker emits (key, value) pairs
    │
    ▼
SHUFFLE: group by key across the cluster
    │
    ▼
REDUCE: per-key aggregation
    │
    ▼
output
```

### Canonical example: word count

```python
def map(document):
    for word in document.split():
        emit(word, 1)

def reduce(word, counts):
    emit(word, sum(counts))
```

### Properties

- **Latency:** minutes to hours
- **Throughput:** very high — designed for full-dataset scans
- **Fault tolerance:** automatic — failed worker's chunk is re-run elsewhere
- **Best for:** ETL into a data warehouse, report generation, training data prep, recompute jobs

### Modern equivalents

Raw MapReduce (Hadoop) is mostly historical. Today's equivalents:
- **Spark** — in-memory, DAG-based; vastly faster than disk-based MapReduce
- **BigQuery / Snowflake / Redshift** — managed SQL over the same primitives
- **Airflow / dbt** — orchestrate batch DAGs

## Stream Processing

Process each event as it arrives, with state held in memory or in a fast store.

```
event stream ──> processor ──> output
              (Kafka, Kinesis)  (Flink, Spark Streaming,
                                 Kafka Streams)
```

### Properties

- **Latency:** sub-second to seconds
- **Throughput:** very high, but per-event work must stay small
- **Fault tolerance:** harder — exactly-once requires checkpointing + transactional sinks
- **Best for:** real-time dashboards, fraud detection, alerting, anomaly detection, feature pipelines for online ML

### Delivery Semantics

| Semantic | Meaning | Cost |
|---|---|---|
| **At-most-once** | Events may be dropped, never duplicated | No retries; lossy on failure |
| **At-least-once** | Events never dropped, may be duplicated | Default in most systems; consumers must be idempotent |
| **Exactly-once** | Each event processed exactly once | Requires transactional sinks + checkpointing; Flink and Kafka Streams support it |

### Windowing

Stream pipelines aggregate over time windows:

| Window type | Definition | Example |
|---|---|---|
| **Tumbling** | Fixed-size, non-overlapping | Count events per 1-minute bucket |
| **Sliding** | Fixed-size, overlapping by some offset | 5-min average updated every 30s |
| **Session** | Bounded by inactivity gap | User session = events grouped until 30 min idle |

**Late events** (data arriving after the window closes due to clock skew or network delay) are the perennial headache. Watermarks define how late is too late; allowed lateness re-opens closed windows for stragglers.

## Lambda Architecture

Run both pipelines in parallel; serve a unified view.

```
                ┌──── Batch layer ────┐
   raw events ──┤   (Spark, daily)    │── correct, slow
                │   → batch view      │
                └─────────────────────┘
                          │
                          ▼     ┌─────────────────┐
                       merge ──>│  Serving layer  │── queries
                          ▲     └─────────────────┘
                          │
                ┌──── Speed layer ────┐
   raw events ──┤   (Flink, real-time)│── approximate, fast
                │   → real-time view  │
                └─────────────────────┘
```

### Why It Exists

Originally proposed because early stream processors couldn't be trusted for correctness. The batch layer was the "source of truth," the speed layer filled the gap until batch caught up.

### The Cost

- **Two codebases** doing the same logic in two frameworks (Spark + Flink)
- **Drift** — speed and batch implementations disagree on edge cases
- **Operational complexity** — two pipelines to monitor, debug, evolve

## Kappa Architecture (the Modern Take)

Run stream-only. When you need to recompute, replay events from the log.

```
   raw events (Kafka, retained forever)
            │
            ▼
       stream processor (Flink)
            │
            ▼
       serving layer
            │
            ▼
       queries
```

- **One codebase**
- **Reprocessing** = restart the stream from an old offset
- Works because modern stream processors (Flink, Kafka Streams) reached batch-quality correctness, and storage got cheap enough to retain raw logs

Kappa has largely replaced Lambda in greenfield designs. Lambda lives on in legacy systems that pre-date trustworthy exactly-once stream processing.

## Choosing

```
Latency budget = hours?  → MapReduce / Spark batch
Latency budget = seconds? → Stream processing
Need both fresh + correct? Built recently? → Kappa (stream + replay)
Built before 2018, stuck with two pipelines? → Lambda
```

Don't add a stream pipeline because it's exciting. Adding stream processing means: event log, exactly-once handling, watermarks, late events, state management, idempotent sinks. Batch is cheaper.

## Related

- [CDC](database/cdc.md) — turning DB writes into a stream pipeline's input
- [Event-driven systems](event-driven.md) — the broader pattern stream processing sits inside
- [Distributed system failure modes](distributed-system-failure-modes.md) — queue depth, oldest-message age (stream backpressure)

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-06-04
**Tags:** data-processing, mapreduce, stream-processing, lambda-architecture, kappa-architecture, flink, spark, kafka, batch, real-time, system-design
