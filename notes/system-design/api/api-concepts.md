# API Concepts Every Software Engineer Should Know

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

- **REST** -- resource-oriented, HTTP-native, most common
- **RPC** -- action-oriented remote procedure calls
- **gRPC** -- high-performance binary protocol (Protocol Buffers)
- **GraphQL** -- client-specified queries, single endpoint
- **WebSockets** -- full-duplex persistent connections
- **Webhooks** -- server-to-client push notifications
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

- **Timeouts** -- preventing hung connections
- **Retries with Backoff & Jitter** -- graceful failure recovery
- **Idempotency (Keys)** -- safe retries without duplicate side effects
- **Rate Limiting, Throttling & Quotas** -- protecting against abuse
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

**Source:** https://blog.bytebytego.com/i/195380781/api-concepts-every-software-engineer-should-know
**Date:** 2026-05-31
**Tags:** api, api-design, rest, graphql, grpc, security, system-design
