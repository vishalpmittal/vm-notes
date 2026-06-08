# API Concepts Every Software Engineer Should Know

> **This is the index.** Each section below lists topics; deep-dives live in sibling files. Drill into:
>
> - **[REST vs GraphQL vs gRPC](rest-vs-graphql-vs-grpc.md)** — when to pick which style
> - **[Real-time communication](real-time-communication.md)** — polling, long polling, SSE, webhooks
> - **[Retries](retries.md)** — exponential backoff, jitter, idempotency, retry budgets, circuit breakers

## Key Takeaways

- API design spans six interconnected dimensions: foundations, styles/patterns, reliability, security, design/evolution, and observability
- Getting HTTP basics wrong (methods, status codes, content negotiation) makes the entire API feel broken regardless of architecture choice
- Security (OAuth, JWTs, scopes) and reliability (idempotency, rate limits, retries) are often afterthoughts but cause the most costly production issues
- Design decisions like naming, pagination, versioning, and backward compatibility determine long-term maintainability
- Documentation, OpenAPI specs, and contract testing are what let other teams confidently integrate with your API

![API Concepts Overview](../../images/20260531-1250-api-concepts-overview.png)

## Foundations

The HTTP basics that determine whether an API feels intuitive or confusing:

- **API Contract** -- the agreement between client and server on request/response shape
- **Endpoints & URI Design** -- resource-oriented URL structure
- **HTTP Methods** -- GET, POST, PUT, PATCH, DELETE and their semantics
- **HTTP Status Codes** -- proper use of 2xx, 4xx, 5xx ranges
- **Request-Response Structure** -- headers, body, query params
- **Media Types & Content Negotiation** -- Accept/Content-Type headers
- **Statelessness** -- each request carries all context needed

## API Styles & Patterns

Different architectural approaches suited to different situations:

- **REST** -- resource-oriented, HTTP-native, most common → see [rest-vs-graphql-vs-grpc.md](rest-vs-graphql-vs-grpc.md)
- **RPC** -- action-oriented remote procedure calls
- **gRPC** -- high-performance binary protocol (Protocol Buffers) → see [rest-vs-graphql-vs-grpc.md](rest-vs-graphql-vs-grpc.md)
- **GraphQL** -- client-specified queries, single endpoint → see [rest-vs-graphql-vs-grpc.md](rest-vs-graphql-vs-grpc.md)
- **WebSockets** -- full-duplex persistent connections
- **Webhooks** -- server-to-client push notifications → see [real-time-communication.md](real-time-communication.md)
- **SSE / Polling / Long Polling** -- server-to-client update patterns → see [real-time-communication.md](real-time-communication.md)
- **Sync vs Async & Long-Running APIs** -- request/response vs event-driven patterns

## Design & Evolution

Naming, structure, and versioning decisions that affect long-term usability:

- **Resource Modeling** -- mapping domain objects to API resources
- **Naming Consistency** -- predictable, intuitive endpoint names
- **Pagination** -- cursor-based vs offset-based for large collections
- **Filtering, Sorting & Field Selection** -- query flexibility
- **Error Handling & Problem Details** -- structured error responses (RFC 7807)
- **Conditional Requests & Optimistic Concurrency** -- ETags, If-Match headers
- **API Versioning** -- URL path, header, or query param strategies
- **Backward Compatibility & Deprecation** -- evolving without breaking clients

## Auth & Security

Getting authentication and authorization right to avoid costly mistakes:

- **Authentication vs Authorization** -- identity vs permissions
- **API Keys** -- simple but limited access tokens
- **OAuth 2.0** -- delegated authorization framework
- **JWTs & Token Validation** -- stateless token verification
- **OpenID Connect** -- identity layer on top of OAuth
- **Scopes, Roles & Permissions** -- fine-grained access control
- **Object-Level Authorization** -- per-resource access checks
- **TLS / HTTPS** -- transport-layer encryption
- **Input Validation** -- sanitizing and validating all inputs

## Reliability & Performance

Easy to ignore until the system is under pressure:

- **Timeouts** -- preventing hung connections → see [retries.md](retries.md)
- **Retries with Backoff & Jitter** -- graceful failure recovery → see [retries.md](retries.md)
- **Idempotency (Keys)** -- safe retries without duplicate side effects → see [retries.md](retries.md)
- **Circuit Breakers** -- stop calling sustained-failing dependencies → see [retries.md](retries.md) and [../circuit-breakers.md](../circuit-breakers.md)
- **Rate Limiting, Throttling & Quotas** -- protecting against abuse → see [../rate-limiting.md](../rate-limiting.md)
- **HTTP Caching** -- Cache-Control, ETags, conditional requests

## Contracts, Testing & Docs

Infrastructure that enables teams to confidently integrate:

- **OpenAPI Specification** -- machine-readable API definition
- **Schemas & Validation** -- request/response schema enforcement
- **Contract Testing** -- verifying provider-consumer agreements
- **Documentation & Discoverability** -- developer experience

## Ops & Observability

Monitoring and managing APIs in production:

- **API Observability** -- metrics, logging, tracing
- **API Gateway** -- centralized routing, auth, rate limiting

---

## REST's Six Architectural Constraints (Fielding, 2000)

The original REST dissertation defines REST through six constraints that explain *why* RESTful APIs scale and stay debuggable, not just what they look like:

1. **Client-server separation** — clear interface boundary
2. **Statelessness** — every request carries everything needed; servers don't track session state
3. **Cacheability** — responses must declare cacheability; intermediaries (CDNs, proxies) can store them
4. **Uniform interface** — identification of resources, manipulation through representations, self-descriptive messages, HATEOAS
5. **Layered system** — clients can't tell whether they're talking to the origin or a proxy/cache/gateway
6. **Code-on-demand** (optional) — servers can transfer executable code (rarely used)

Most "REST" APIs in practice satisfy constraints 1-3 and partially satisfy 4 (usually skipping HATEOAS). The constraints, more than the URL conventions, are what make REST work at internet scale.

### REST's Real Weaknesses

The honest list:
- **Over-fetching / under-fetching** — fixed resource shapes force clients to take more or less than they need
- **Multiple round-trips** — fetching a list-then-details often requires N+1 requests
- **Loose JSON contracts** — no protocol-level enforcement (vs gRPC's proto)
- **Versioning friction** — `/v2/...` is a workaround, not a solution
- **Real-time gaps** — REST is request/response only; live updates need [SSE or WebSockets](real-time-communication.md)

REST is the **path of least resistance** for resource-centric domains, not universally optimal. See [rest-vs-graphql-vs-grpc.md](rest-vs-graphql-vs-grpc.md) for the layered choice.

---

**Source:** https://blog.bytebytego.com/i/195380781/api-concepts-every-software-engineer-should-know
**Source:** https://blog.levelupcoding.com/p/rest-apis-properly-explained
**Date:** 2026-05-31 (initial), 2026-06-05 (added REST six constraints + honest weaknesses)
**Tags:** api, api-design, rest, graphql, grpc, security, system-design, fielding-constraints, hateoas
