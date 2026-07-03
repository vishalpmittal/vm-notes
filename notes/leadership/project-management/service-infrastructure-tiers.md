# Service Infrastructure Tiers

## Key Takeaways
- Services are classified into two infrastructure tiers based on maturity: production (GA) with dedicated infrastructure and experimental (pre-GA) with shared infrastructure.
- Experimental services deliberately use shared infrastructure to minimize overhead and make it easy to sunset failed experiments.
- Production services require dedicated infrastructure: dedicated databases, independent CI/CD pipelines, service-specific dashboards, SLOs, and off-hours alerting.
- A single on-call rotation covers all services regardless of tier — no separate rotation for experimental work.
- When an experimental service graduates to production, a formal infrastructure migration checklist applies.

## Service Tiers

### Production Services (GA)

Services that have reached general availability require dedicated infrastructure for each concern:

| Concern | Requirement |
|---|---|
| Databases | Dedicated instance(s) |
| Deployment pipeline | Independent, service-specific CI/CD |
| Monitoring | Service-specific dashboards with defined SLOs |
| Alerting | Off-hours alerting enabled |
| On-call | Shared team on-call rotation |

### Experimental Services (Pre-GA)

Experimental services run on shared infrastructure to keep experimentation costs and operational overhead low:

| Concern | Requirement |
|---|---|
| Databases | Shared schema or shared instance |
| Deployment pipeline | Shared pipeline |
| Monitoring | Basic health checks only |
| Alerting | Business-hours only |
| On-call | Shared team on-call rotation |

> Experimental services run within shared infrastructure rather than standing up dedicated resources for each experiment. This keeps the cost of experimentation low and avoids operational overhead for projects that may be sunset.

## On-Call Unification

The same on-call rotation covers both production and experimental services. This avoids maintaining separate rotations for experimental work, at the cost of requiring on-call engineers to be familiar with both tiers.

## Infrastructure Graduation Checklist

When a service graduates from experimental to production, complete the following migration:

- [ ] Provision dedicated database instance(s)
- [ ] Set up an independent deployment pipeline
- [ ] Create service-specific monitoring dashboards with defined SLOs
- [ ] Enable off-hours alerting

See also: [Project Categories and Prioritization](./project-categories-and-prioritization.md) for the graduation evaluation criteria.

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** service-lifecycle, production-readiness, experimentation, ci-cd, monitoring, on-call, slo, infrastructure
