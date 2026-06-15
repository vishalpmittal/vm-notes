# TODO — Topics to Study

Items collected from imported docs that aren't yet covered in this repo. Categorized by area.

> **Format:** `- [ ] **Topic** — short note on what to cover`. Add `**target file:**` once decided.
>
> **Source of items:** the TBD/parking-lot sections of imported personal docs. Most existing notes are not re-listed here — this is the gap list.

## System Design

See also: **[system-design/to-do.md](system-design/to-do.md)** for the larger system-design backlog (databases, message brokers, architecture patterns, networking, reliability) that was already cataloged before this import.

### Architecture Patterns

- [ ] **Ambassador pattern** — sidecar that proxies network requests on behalf of an app (smart client → ambassador → service). Common in service mesh world
- [ ] **Publisher/Subscriber pattern** — separate from event-driven generally; focused-pattern note worth having
- [ ] **Helix** — Apache Helix distributed cluster management framework. Used for partition assignment, leader election, replica placement (LinkedIn-origin)
- [ ] **API Gateway patterns** — aggregation, BFF, rate-limit-at-edge, auth-at-edge. Partially in `forward-reverse-proxy-and-api-gateway.md` — focused gateway-patterns note worth having

### Networking

- [ ] **Ingress vs. Egress** — controlling traffic flowing *in* vs *out* of a network/cluster. K8s ingress controllers; egress filtering / proxy patterns
- [ ] **Firewall** — L3/L4 vs L7 firewalls, stateful vs stateless, common deployment patterns (perimeter, host-based, cloud security groups)
- [ ] **VPN** — site-to-site vs remote-access, IPsec vs WireGuard, split tunneling, when VPN ≠ Zero Trust solution
- [ ] **NGINX deep dive** — reverse proxy + load balancer + cache + WAF; standard config patterns. (Mentioned in `load-balancer.md` but no focused note)
- [ ] **Envoy** — modern L7 proxy used in service mesh (Istio, Consul); xDS API; lessons from Lyft scale

### Distributed Patterns (Top 7 reference)

From [Top 7 Most-Used Distributed System Patterns](https://www.youtube.com/watch?v=nH4qjmP2KEE) — the ones not yet covered:

- [ ] **Ambassador** — see Architecture Patterns above
- [ ] **Publisher/Subscriber** — see Architecture Patterns above

(Already covered: Circuit Breaker → `circuit-breakers.md`, CQRS → `database/event-sourcing-cqrs.md`, Event Sourcing → same, Sharding → `database/replication-and-sharding.md`)

### Observability

- [ ] **Notification systems** — Pager Duty, Slack, Jira, ServiceNow, Blameless integration patterns. Alert routing, on-call rotations, incident response
- [ ] **ELK stack** — Elasticsearch + Logstash + Kibana for log aggregation. Architecture, ingestion patterns, retention. (Only mentioned in passing in source)

## Security

- [ ] **Vault** — HashiCorp Vault for secrets management: storing and controlling access to tokens, passwords, certificates, encryption keys, env vars. Privilege access management. Sealing/unsealing, dynamic secrets, transit engine
- [ ] **SPIFFE / SPIRE / SVID** — Secure Production Identity Framework For Everyone — cryptographic workload identity standard. SPIRE is the runtime; SVID is the identity document. Foundational for service-mesh mTLS and Zero Trust
- [ ] **PAM (Privileged Access Management) deep dive** — adjacent to `zero-trust-and-jit-access.md` but worth a focused product-landscape note (CyberArk, BeyondTrust, Teleport)

## Infra & DevOps

- [ ] **HPA (Horizontal Pod Autoscaler)** — K8s metric-driven pod scaling. Touched in `docker-vs-kubernetes.md` but worth a focused note on metrics selection, cooldowns, custom metrics via Prometheus
- [ ] **Liquibase deep dive** — DB schema migrations, rollbacks, versioning. Vs Flyway, vs ORM migrations. (Mentioned in source as one-liner)
- [ ] **Terraform vs CloudFormation** — IaC tooling deep comparison. State management, drift detection, modules. Touched in `infrastructure-as-code.md` but deeper treatment worth having
- [ ] **Ansible vs Chef vs Puppet** — agentless vs agent-based, push vs pull, when each wins. Mostly Ansible relevant in 2026
- [ ] **Jenkins deep dive** — pipeline-as-code, declarative vs scripted, common patterns. Vs GitHub Actions / GitLab CI

## Programming

- [ ] **ACID — full deep dive** — only Atomic was covered in source (with SQL example). Consistent, Isolated, Durable each warrant treatment. Pair with isolation levels (Read Committed, Repeatable Read, Serializable, Snapshot) → already in `system-design/to-do.md`
- [ ] **Monitors and synchronization primitives** — language-level constructs (Java `synchronized`, C# `lock`, Python `threading.Lock`), semaphores, condition variables. (Covered conceptually in `programming/concurrency-fundamentals.md`; deeper language-specific treatment optional)

## AI/ML & Agents

*(No new items from this import. See `notes/ai-ml-ds/` for existing notes.)*

## Leadership

*(No new items from this import. See `notes/leadership/` for existing notes.)*

## Product & Design

*(No new items from this import.)*

## Life Coaching

*(No new items from this import.)*

## Other

- [ ] **Load Balancer vs Reverse Proxy vs API Gateway — focused comparison** — overlapping concepts that confuse interviews. Partially covered in `forward-reverse-proxy-and-api-gateway.md`; explicit side-by-side may be worth promoting

---

**Source:** /Users/vimittal/Downloads/prep/prep.html (TBD section + trailing fragments)
**Date:** 2026-06-13
**Tags:** todo, study-list, gaps, parking-lot
