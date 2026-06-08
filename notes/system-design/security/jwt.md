# JSON Web Tokens (JWT)

The compact, self-contained, signed token format underneath OIDC, SSO, and most modern API auth — plus the failure modes that cause most of the production incidents involving JWTs.

## Key Takeaways

- A JWT is three base64-URL-encoded parts joined by dots: **header.payload.signature**. The signature is the *only* thing that makes it trustworthy
- **Signed ≠ encrypted.** Anyone can decode and read the payload. Don't put secrets in there
- **Validation must check signature + exp + iss + aud** — not just signature. Many production JWT bugs are missing one of these checks
- **JWTs can't be revoked instantly.** Once issued, they're valid until they expire. This is why production systems use **short-lived JWTs + refresh tokens** + a deny-list for emergencies
- The most catastrophic JWT bug is **`alg: none`** — historically some libraries would accept tokens that claimed no algorithm and skip signature validation entirely

## Anatomy

A JWT looks like:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjMiLCJleHAiOjE3MzM0MDB9.4LX...
└──────── header ─────────┘ └────────── payload ──────────┘ └─ signature ─┘
```

### Header
Identifies token type and signing algorithm:
```json
{ "alg": "HS256", "typ": "JWT" }
```

Common `alg` values:
- **HS256** — HMAC-SHA256 (symmetric — same secret signs and verifies)
- **RS256** — RSA SHA256 (asymmetric — private key signs, public key verifies; preferred for distributed systems)
- **ES256** — ECDSA SHA256 (asymmetric, smaller keys; modern default)

### Payload
Claims about the user/session. Standard ones:

| Claim | Meaning |
|---|---|
| `iss` | Issuer — who minted the token |
| `sub` | Subject — usually the user ID |
| `aud` | Audience — who the token is intended for |
| `exp` | Expiration time (Unix timestamp) |
| `iat` | Issued at |
| `nbf` | Not before — token isn't valid until this time |
| `jti` | JWT ID — unique identifier for revocation |

Plus custom claims:
```json
{ "sub": "user-123", "role": "admin", "tenant": "acme" }
```

### Signature
The cryptographic proof. Computed as:
```
HMACSHA256(
  base64url(header) + "." + base64url(payload),
  secret
)
```

Or with RS256, sign with the private key; verify with the public key.

## Validation Process

A service receiving a JWT must:

1. **Parse** the three parts
2. **Verify the signature** using the issuer's secret/public key
3. **Check expiration** (`exp` > current time)
4. **Check issuer** (`iss` matches expected issuer)
5. **Check audience** (`aud` matches this service)
6. **Check not-before** (`nbf` <= current time, if present)
7. **Optionally check revocation** (`jti` not in deny-list)

Skipping any of steps 2-5 is a common production bug. The signature check alone says "this token came from someone who knows the secret" — not "this token is valid for this service right now."

## "Signed, Not Encrypted"

A JWT is **readable by anyone** who has the token string. Base64-decode the payload and you see the claims in plaintext.

The signature only proves:
- The token was issued by someone with the signing key
- The token hasn't been tampered with since signing

It does NOT hide the contents. So:

❌ Don't put passwords, API keys, or PII in the payload
❌ Don't put internal IDs you'd treat as secrets
✅ Do put: user ID, roles, expiration, audience — things the user can already see about their own session

If you need encrypted tokens, use **JWE** (JSON Web Encryption) — a different spec on top of JOSE that encrypts the payload. Most "JWTs" in practice are JWS (signed only).

## Common Anti-Patterns

### 1. The `alg: none` Vulnerability (Historical, Still Found)

Some early JWT libraries would accept `alg: none` and skip signature validation. An attacker crafts a token with `alg: none`, no signature, and arbitrary claims:

```json
{ "alg": "none" }.{ "sub": "admin", "role": "superuser" }.
```

If your library accepts this, anyone can be admin. Modern libraries reject this; ancient or unmaintained ones may not. **Always pin the expected algorithm in your verifier.**

### 2. Algorithm Confusion (HS256 vs RS256)

Some libraries let attackers swap RS256 → HS256, then use the *public key* as the HMAC secret. If the verifier trusts the algorithm in the header, the attacker can forge tokens. **Pin the expected algorithm; don't accept whatever the header says.**

### 3. Missing `aud` Check

You verify signature + expiration. Token is valid! ...for another service. An attacker reuses a token issued for Service B against Service A. **Always check `aud`.**

### 4. Trusting Claims You Don't Control

If your JWT includes `role: admin` and you trust it without re-checking against your authorization system, you're delegating authz decisions to whatever system issued the token. Sometimes intended, often not. **Be deliberate about which claims are authoritative.**

### 5. Long Expirations Without Refresh

If your JWT lasts 30 days, a leaked token is valid for 30 days. **Short-lived JWTs (5-15 minutes) + refresh tokens** is the production pattern.

### 6. Storing JWTs in localStorage

XSS that reads localStorage steals the token. Use **HttpOnly cookies** for browser-served JWTs so JavaScript can't read them. See [common-cyber-attacks § XSS](common-cyber-attacks.md#5-cross-site-scripting-xss).

### 7. Including Sensitive Data in Payload

JWTs are readable. PII, internal API keys, secrets — anything in the payload is visible. Use JWE if you need encryption, or just don't put secrets in there.

## Revocation: The Hard Part

The promise of JWTs is statelessness — the server doesn't need to look anything up. The downside: **once issued, a JWT is valid until it expires**. There's no instant way to invalidate a leaked token.

Strategies:

| Strategy | How | Trade-off |
|---|---|---|
| **Short expirations** | 5-15 minute tokens | Adds refresh-token complexity |
| **Refresh tokens** | Long-lived refresh + short-lived JWT | Refresh tokens *can* be revoked at the issuer |
| **Deny-list** | Server checks `jti` against a revocation list | Loses statelessness; needs distributed store |
| **Token versioning** | Include a user-version claim; bump on revocation | Centralized; requires reading user state per request |

In practice: short-lived JWTs + refresh tokens + emergency deny-list for compromised users. Pure "JWTs are stateless, so we don't need any state" is a false economy at scale.

## Comparison to Alternatives

| | JWT | Opaque token (random ID) |
|---|---|---|
| **Validation** | Self-contained — verify signature + claims | Look up in token store |
| **Revocation** | Hard (must wait for expiry or maintain deny-list) | Easy (delete from store) |
| **Size** | Larger (signed payload) | Smaller (just an ID) |
| **Read claims** | Anyone can decode (it's signed, not encrypted) | Requires server lookup |
| **Distributed verification** | Easy — share public key | Requires shared token store |
| **Best for** | Microservices, distributed systems | Single-monolith, easier revocation |

Both are valid; the choice depends on whether you can tolerate the revocation lag of JWTs.

## Related

- [OAuth](oauth.md) — JWT is the format for OIDC ID tokens
- [SSO](sso.md) — SAML uses XML signing; OIDC uses JWTs
- [Common cyber attacks § XSS](common-cyber-attacks.md#5-cross-site-scripting-xss) — why HttpOnly cookies matter for JWT storage
- [Agent identity and auth](../../ai-ml-ds/concepts/agent-identity-and-auth.md) — Uber's per-hop JWT exchange for AI agents
- [Hashing vs encryption vs tokenization](hashing-encryption-tokenization.md) — JWT is "signed not encrypted"; this note explains the broader distinction

---

**Source:** https://blog.levelupcoding.com/p/jwt-clearly-explained
**Date:** 2026-06-05
**Tags:** jwt, json-web-token, authentication, oauth, oidc, jwe, jws, security, tokens, hs256, rs256
