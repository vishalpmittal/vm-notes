# Cross-Cutting Concerns in Microservices

## Key Takeaways

- Cross-cutting concerns are secondary functionality (logging, auth, config, monitoring) that affects every service but isn't core business logic — handle them centrally to avoid duplication
- Four key patterns: externalized configuration, service discovery, circuit breaker, and blue-green deployment
- Centralize via API gateways, service meshes, AOP, or sidecars — keep microservices focused on business logic
- See also: [Service Discovery](service-discovery.md), [Circuit Breakers](circuit-breakers.md) for detailed notes on those patterns

## Externalized Configuration

**Problem:** Services call databases and other services with environment-specific URLs/settings (dev, QA, prod). Changing config shouldn't require a rebuild and redeploy.

**Solution:** Store configuration outside application code — in external config files, centralized repositories, or config services.

**Benefits:**

- **Decoupling** — same service runs in dev, staging, prod without code changes
- **Simple modification** — change DB URLs, API keys, feature toggles without restart
- **Centralized management** — one location for all microservice settings (critical at scale with hundreds of services)
- **Dynamic configuration** — adjust settings in response to load or user requests at runtime
- **Environment-specific** — each environment gets its own parameter set without touching code

## Blue-Green Deployment

**Problem:** Deploying updated microservices can cause outages; rollback of multiple services is a nightmare.

**Solution:** Run two identical production environments (Blue = current, Green = new version). Switch traffic after validation; instant rollback by switching back.

**How it works:**

1. **Blue** serves production traffic (current version)
2. **Green** is deployed with updated version, tested and validated
3. **Traffic switch** via load balancer from Blue → Green
4. **Rollback** — if issues emerge, switch traffic back to Blue instantly
5. **Post-deploy** — Green becomes active; Blue stages the next release

**Benefits:** zero downtime, instant rollback, production-like testing, decreased release risk

## Canary Deployment

**Problem:** Blue-green is all-or-nothing — if Green is bad, every user sees it the moment traffic flips. You want to validate the new version with real production traffic *before* exposing everyone.

**Solution:** Deploy the new version alongside the current one, route a small percentage of traffic (1-5%) to it, monitor health metrics, gradually increase if healthy, roll back if not.

**How it works:**

1. Deploy v2 alongside v1 (both running)
2. Route 1-5% of traffic to v2 via load balancer / service mesh
3. Monitor: error rate, p95/p99 latency, business metrics for the canary cohort
4. Auto-rollback if SLO breaches; otherwise increase to 10%, 25%, 50%, 100% over hours/days
5. Once 100%, retire v1

**Canary vs. Blue-Green:**

| | Blue-Green | Canary |
|---|---|---|
| Traffic switch | Instant (0 → 100%) | Gradual (1% → 5% → 25% → 100%) |
| Blast radius if bad | All users immediately | Small cohort first |
| Rollback speed | Instant (switch back) | Pull traffic from canary cohort |
| Requires | Two full environments | One environment with traffic-splitting |
| Best for | Major releases, schema changes | Routine releases, gradual rollouts |

**Requires:** backward compatibility between v1 and v2 (since they run concurrently), traffic-splitting infrastructure (service mesh, smart load balancer, feature flags), and good observability to detect canary regressions.

**Often paired with:** feature flags (deploy code dark, then activate per-user/cohort) for even finer control without redeploying.

## Cross-Cutting Patterns Summary

| Pattern | Solves | Key Mechanism |
|---|---|---|
| Externalized Configuration | Config changes requiring rebuilds | External config stores, environment-specific injection |
| Service Discovery | Services finding each other dynamically | Service registry (Consul, Eureka) or DNS/service mesh |
| Circuit Breaker | Cascading failures from unhealthy dependencies | Three-state proxy (Closed → Open → Half-Open) |
| Blue-Green Deployment | Downtime and risky releases | Dual environments with traffic switching |
| Canary Deployment | Reducing blast radius of bad releases | Gradual traffic shift with monitoring + auto-rollback |

---

**Source:** /Users/vimittal/Downloads/IJIRCT2412070.md
**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-05-29 (initial), 2026-06-04 (added canary)
**Tags:** microservices, cross-cutting-concerns, externalized-configuration, blue-green-deployment, canary-deployment, design-patterns
