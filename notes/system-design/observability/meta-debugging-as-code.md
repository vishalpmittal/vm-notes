# Meta's DrP: Debugging as Code

How Meta turned ad-hoc debugging into engineered software — a platform where 2,000+ "analyzers" run automatically during incidents, reducing mean time to resolve by 20-80% across 300 teams.

## Key Takeaways

- **Debugging expertise codified as software, not tribal knowledge.** Analyzers are testable code components that go through code review and CI/CD like any service
- **50,000 automated analyses per day** across 300 teams, with 2,000+ analyzers in production. Auto-trigger on alerts; emit structured findings
- **Cross-service chaining** lets root-cause analysis traverse microservice boundaries — analyzers compose into investigation pipelines
- **Backtesting at code-review time** verifies a new analyzer would have caught past incidents — analyzers earn their keep before merging
- Deliberately keeps **human judgment in the loop**. DrP surfaces structured findings; humans decide remediation. No auto-rollbacks without approval

## What DrP Is

A platform where engineers codify debugging workflows as **analyzers** — reusable, testable code components that:

- Run automatically when an alert fires
- Pull signals from observability systems
- Apply domain-specific logic (anomaly detection, time-series correlation, dimension analysis)
- Emit structured findings (the SDK provides shared primitives)
- Chain into other analyzers across services for root-cause traversal

The platform handles dispatch, scheduling, output aggregation, and post-processing (e.g., automatically filing bugs, suggesting reversions).

## Why "Debugging as Code" Matters

Traditional debugging is **tribal knowledge** — the senior engineer who knows that "when X service alerts, also check Y queue depth and Z config." That knowledge:

- Lives in heads, dies when the engineer leaves
- Doesn't compose across teams
- Doesn't get tested
- Doesn't improve with feedback

DrP analyzers are **software artifacts**. They get:

- Code reviewed
- Version-controlled
- Tested with backtests against historical incidents
- Composed across team boundaries without manual coordination
- Continuously improved as new failure modes emerge

The 20-80% MTTR improvement comes from this shift in artifact type.

## Architecture Highlights

| Component | Role |
|---|---|
| **SDK** | Shared libraries for anomaly detection, time-series correlation, dimension analysis |
| **Analyzers** | Per-team debugging workflows as code; 2,000+ in production |
| **Auto-trigger** | Alerts dispatch analyzers automatically — no manual "run the debugger" step |
| **Cross-service chaining** | Analyzer A's findings can trigger Analyzer B in another service |
| **Post-processing** | Automated remediation actions: file bugs, suggest reversions, page on-call |
| **DrP Insights** | Periodically analyzes outputs across all investigations to surface top alert causes — meta-analysis of the analyzers themselves |

## Five Novel Decisions

1. **Investigation expertise = engineered software** (not tribal knowledge)
2. **Direct alert-lifecycle integration** — analyzers auto-trigger on alert, not as a separate "run when you remember to" step
3. **Cross-team analyzer composition without coordination** — teams build their own, the platform handles chaining
4. **Backtest at code-review time** — does this analyzer catch past incidents? If not, why are you adding it?
5. **Human judgment in the loop** — DrP surfaces structured findings but doesn't auto-remediate; engineers decide. Prevents auto-remediation from making things worse

## Generalizable Lessons

For organizations smaller than Meta, the transferable principles:

1. **Codify runbooks instead of writing markdown.** A runbook that runs itself is more valuable than one that requires reading
2. **Auto-trigger on alerts.** The signal you want is "what's the analysis result," not "the alert fired, now I'll go look"
3. **Make analyzers composable.** Per-team analyzers that can call each other across service boundaries beat siloed tooling
4. **Backtest new diagnostic logic.** Same discipline as testing application code — does this catch what it's supposed to?
5. **Preserve human judgment for remediation.** Auto-remediation is a separate, harder problem with much higher blast radius

## What This Doesn't Solve

- **Novel failure modes** — analyzers catch what they're written for. New failure modes need new analyzers
- **Bad observability** — DrP is only as good as the underlying signals. If you don't have metrics/logs/traces, no analyzer can read them
- **Organizational learning** — building the analyzer is the first step; getting it adopted across teams requires its own work

## Related

- [SLA vs SLO vs SLI](sla-slo-sli.md) — the metric framework analyzers consume
- [Distributed system failure modes](../distributed-system-failure-modes.md) — the failure modes DrP is detecting
- [Service mesh and sidecar](../service-mesh-and-sidecar.md) — the observability "for free" pattern that DrP analyzers can build on
- [AI-assisted code review](../../ai-ml-ds/concepts/ai-assisted-code-review.md) — the analog for code review (codifying review expertise)

---

**Source:** https://blog.bytebytego.com/p/how-meta-turned-debugging-into-a
**Date:** 2026-06-05
**Tags:** meta, debugging, observability, drp, analyzers, incident-response, mttr, automation, case-study
