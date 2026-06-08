# Agoda's Financial Unified Data Pipeline (FINUDP)

How Agoda built a single source of truth for financial reporting after years of conflicting metrics across teams — by consolidating multiple independent pipelines into one Spark-based pipeline with data contracts, shadow testing, and tiered alerting.

## Key Takeaways

- **The triggering problem**: multiple independent pipelines created duplicate sources, conflicting definitions, and no single source of truth for financial reporting (sales, costs, revenue, margins)
- **FINUDP** consolidates onto Apache Spark with a centralized execution layer, source tables from booking/payment systems, validation, and downstream consumption by Finance/Planning/Ledger teams
- **Shadow testing** — run new pipeline versions in parallel against production data, compare to old pipeline before cutover
- **Multi-tiered alerting** (email → Slack → NOC) prevents alert fatigue while ensuring critical issues escalate
- The trade-off: **slower development + stricter governance** accepted for **consistency + reliability + unified metrics**. For financial data, the trade is worth it

## The Problem: Multiple Sources of Truth

Before FINUDP, Agoda's financial data was scattered across many pipelines. Symptoms:

- Different teams reporting different revenue numbers for the same period
- Duplicate data sources where the "right" one was unclear
- Conflicting definitions of "booking", "revenue", "margin"
- Poor data quality control — bugs in one pipeline silently propagated

For financial data, this is unacceptable — auditors, regulators, executives all need numbers that agree.

## Architecture

```
Booking systems   Payment systems
        ↓               ↓
  Source tables (the raw events)
        ↓
  FINUDP (centralized Spark execution layer)
        ↓
  Data lake with validation (Quilliup for source-to-target validation)
        ↓
  Downstream consumers: Finance, Planning, Ledger teams
```

### Key Components

| Component | Role |
|---|---|
| **Apache Spark** | Distributed processing engine |
| **Centralized execution layer** | One place where transformations happen — vs scattered jobs |
| **GoFresh** | Monitors data refresh cycles (hourly) |
| **Quilliup** | Source-to-target validation (does the output match what the input implies?) |
| **Data contracts** | Negotiated with upstream teams — schema and semantics |
| **Multi-level alerts** | Email → Slack → NOC depending on severity |

## Scale and Reliability

- Processes **millions of financial data points daily**
- Hourly refresh cycles
- **95.6% uptime** currently; **99.5% target**
- Reduced runtime from **5 hours to 30 minutes** through optimization

The uptime gap between 95.6% and 99.5% is significant for financial data. Reaching 99.5% requires the kind of operational discipline FINUDP is designed for (data contracts, shadow testing, multi-tiered alerts).

## The Two Key Discipline Mechanisms

### Shadow Testing

Run the new pipeline version **alongside the production pipeline** on the same input data. Compare outputs. Only cut over once they match (or once you understand and accept the differences).

This is the safe alternative to "deploy and watch." For financial data, you can't risk a regression — shadow testing makes regressions detectable *before* they hit production reports.

### Data Contracts with Upstream Teams

The contract is explicit:
- Schema (fields, types, nullability)
- Semantics (what does this field mean, what are valid values)
- SLAs (how often does it update, what's the staleness budget)
- Breaking change protocol (how do upstream teams notify before changes)

Without contracts, schema changes upstream silently break downstream pipelines. With contracts, breaking changes get caught at deploy time or at least flagged for negotiation.

This is the data-engineering analog to API contracts and schema registries (see [event-driven.md § schema evolution](../event-driven.md)).

## The Trade-offs Accepted

> "Slower development velocity and stricter governance requirements accepted for consistency, reliability, and unified metrics governance."

What FINUDP gives up:
- **Speed** — adding a new metric requires going through the unified pipeline, not spinning up a side script
- **Local autonomy** — teams can't have their own definitions; they have to align
- **Flexibility** — governance approvals slow down ad-hoc analysis

What FINUDP gains:
- **Consistency** — all reports agree
- **Reliability** — one pipeline to monitor, fix, optimize
- **Auditability** — provenance for every number
- **Quality** — validation catches bad data before reports

For financial data this trade is obvious. For other data domains (analytics, ML features), the right balance differs — see [data-warehouse-vs-data-lake-vs-data-mesh.md](data-warehouse-vs-data-lake-vs-data-mesh.md) for the broader spectrum.

## The Generalizable Pattern

For any team where multiple sources of truth are a problem:

1. **Centralize execution.** One pipeline, not many
2. **Data contracts with upstream.** Schema + semantics + SLAs, written down
3. **Validation at the boundary.** Source-to-target checks; trust nothing
4. **Shadow test new versions.** Compare against production before cutover
5. **Tier your alerts.** Not everything is page-the-NOC; route severity appropriately
6. **Optimize for runtime.** Slow pipelines = stale data = decision-making with last-week's numbers (Agoda cut 5h → 30min)
7. **Accept the velocity trade-off.** Governance costs speed; for high-trust data domains, it's the right trade

## Related

- [CDC](cdc.md) — adjacent pattern for moving data; FINUDP is at the analytical layer rather than transactional
- [Datadog data replication](datadog-data-replication.md) — same era, similar discipline around schema and provisioning
- [Data warehouse vs lake vs mesh](data-warehouse-vs-data-lake-vs-data-mesh.md) — the broader architectural space FINUDP sits in
- [Event-driven systems § schema evolution](../event-driven.md) — schemas-as-contracts in the streaming context
- [Cloud-native data architecture](cloud-native-data-architecture.md) — broader cloud data patterns

---

**Source:** https://blog.bytebytego.com/p/how-agoda-built-a-single-source-of
**Date:** 2026-06-05
**Tags:** agoda, financial-data, single-source-of-truth, finudp, spark, data-pipeline, data-contracts, shadow-testing, governance, case-study
