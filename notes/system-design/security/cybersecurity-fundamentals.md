# Cybersecurity Fundamentals

The conceptual scaffolding that the rest of the security notes in this folder hang from — CIA triad, threat modeling (STRIDE), defense-in-depth, and the categories of control that show up everywhere.

## Key Takeaways

- The **CIA triad** (Confidentiality, Integrity, Availability) is the canonical framing for what security controls protect — every security decision can be classified by which leg(s) of the triad it addresses
- **Threat modeling with STRIDE** (Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege) gives a systematic way to think adversarially during design
- **Defense-in-depth** is the operating principle: no single control suffices. Layer encryption, authentication, authorization, network controls, and application defenses
- Five canonical control categories cover most security work: **identity & access, cryptographic, network, application, infrastructure**
- The cultural shift is **thinking adversarially during development** — security can't be a phase or a separate team's problem at scale

## The CIA Triad

The three pillars that nearly every security concept maps to:

| Pillar | What it means | What protects it |
|---|---|---|
| **Confidentiality** | Data is seen only by those allowed to see it | Encryption, access controls, authentication |
| **Integrity** | Data remains unchanged (or changes are detectable and authorized) | Signatures, hashes, audit logs, transactions |
| **Availability** | Authorized users can access systems when needed | Redundancy, DDoS protection, rate limiting, capacity planning |

A useful diagnostic: when designing a control, ask "which leg(s) of CIA does this protect?" If you can't answer, the control may not be earning its complexity.

The triad also surfaces trade-offs: encryption (confidentiality) costs CPU/latency (availability). Strict authorization (confidentiality) costs user friction (effectively availability).

## Threat Modeling with STRIDE

Microsoft's STRIDE framework — six categories of threats to consider when modeling a system:

| Letter | Threat | Defends against |
|---|---|---|
| **S**poofing | Pretending to be someone else | Authentication (passwords, MFA, certificates) |
| **T**ampering | Modifying data in transit or at rest | Integrity (signatures, hashes, TLS) |
| **R**epudiation | Denying you did an action | Audit logs, signed actions, non-repudiation |
| **I**nformation disclosure | Leaking confidential data | Encryption, access controls, redaction |
| **D**enial of service | Making the system unavailable | Rate limiting, redundancy, DDoS protection |
| **E**levation of privilege | Gaining unauthorized capabilities | Least privilege, authorization checks |

For each component / interface / data flow in a system, ask: which of these threats applies? You'll often find STRIDE forces you to confront threats that "feel obvious in hindsight but weren't on the radar."

## Defense-in-Depth

The operating principle that pervades every other security topic:

> No single defense suffices. Systems require overlapping safeguards.

Concrete pattern: don't rely on "the firewall keeps attackers out." Also encrypt data at rest. Also authenticate every API call. Also use least-privilege DB accounts. Also validate every input. Each layer catches what the previous layer missed.

Why this works:
- **Attackers exploit small gaps methodically** — they don't need to break every layer, just find a path through
- Layers that look redundant aren't — they fail for different reasons
- Cost of a layer is small; cost of a single layer failing without backup is catastrophic

## Five Control Categories

### 1. Identity & Access
Who you are, and what you can do.

- Authentication ([password storage and hashing](password-storage-hashing.md), MFA, [SSO](sso.md))
- Authorization (RBAC, ABAC, ReBAC — see [google-zanzibar.md](google-zanzibar.md))
- Least privilege — only the access required for the task

### 2. Cryptographic
Math that protects data.

- Symmetric encryption (AES-256-GCM, ChaCha20)
- Asymmetric encryption (RSA, ECC)
- Hashing (Argon2, bcrypt, SHA-256)
- TLS for transport
- See [hashing-encryption-tokenization.md](hashing-encryption-tokenization.md) for picking among them

### 3. Network
Boundaries and traffic.

- Firewalls (ingress, egress)
- Zero-trust architectures (no implicit trust based on network location)
- VPNs (less popular in zero-trust world)
- Network segmentation (limit lateral movement)
- mTLS service-to-service ([service-mesh-and-sidecar.md](../service-mesh-and-sidecar.md))

### 4. Application
Defenses inside the code.

- Input validation (the highest-leverage control surface; see [common-cyber-attacks.md](common-cyber-attacks.md))
- Output encoding (XSS defense)
- Parameterized queries (SQLi defense)
- CSRF tokens
- Content Security Policy
- SSRF defenses (validate URLs, restrict outbound)

### 5. Infrastructure
The substrate.

- IAM policies (cloud account control)
- Secrets management (Vault, KMS — never in env vars committed to git)
- Container security (image scanning, runtime policies)
- Patch management (most "zero-days" exploited at scale are actually known CVEs with available patches)
- Logging + observability (you can't respond to incidents you can't see)

## The Adversarial Mindset

The meta-lesson: engineers must **think adversarially during development**, not assume security is "someone else's problem."

Concrete practices:
- During design, ask "how would I break this?"
- For each input, ask "what's the worst payload?"
- For each external dependency, ask "what if it's compromised?"
- For each permission grant, ask "if this leaks, what's the blast radius?"

This is the cultural shift behind shift-left security: moving security thinking into design and development phases rather than relying on post-hoc audits.

## How the Rest of This Folder Connects

This note is the index for the security/ folder. Specific topics:

| Topic | Where |
|---|---|
| Password storage | [password-storage-hashing.md](password-storage-hashing.md) |
| Password attacks | [password-attacks.md](password-attacks.md) |
| Common web attacks | [common-cyber-attacks.md](common-cyber-attacks.md) |
| OAuth (authorization) | [oauth.md](oauth.md) |
| SSO (authentication delegation) | [sso.md](sso.md) |
| JWT (token format) | [jwt.md](jwt.md) |
| Hashing vs encryption vs tokenization | [hashing-encryption-tokenization.md](hashing-encryption-tokenization.md) |
| Google Zanzibar (authorization at scale) | [google-zanzibar.md](google-zanzibar.md) |
| Agent identity and auth (AI agents) | [../../ai-ml-ds/concepts/agent-identity-and-auth.md](../../ai-ml-ds/concepts/agent-identity-and-auth.md) |
| GitHub agentic workflow security | [github-agentic-workflow-security.md](github-agentic-workflow-security.md) |
| Stripe fraud detection | [stripe-fraud-detection.md](stripe-fraud-detection.md) |
| Predictive analytics ethics | [../../ai-ml-ds/ethics/predictive-analytics-and-ethics.md](../../ai-ml-ds/ethics/predictive-analytics-and-ethics.md) |

## Related

- [Distributed system failure modes](../distributed-system-failure-modes.md) — availability failures are also security incidents
- [Service mesh and sidecar](../service-mesh-and-sidecar.md) — mTLS as a network-layer control
- [Prompt injection defenses](../../ai-ml-ds/concepts/prompt-injection-defenses.md) — STRIDE-style threat modeling for LLM systems
- [MFA](mfa.md) — strong identity is the foundation for everything else
- [Zero Trust + JIT Access](zero-trust-and-jit-access.md) — "assume breach" + time-bounded privilege
- [TLS / SSL](tls-ssl.md) — the encryption layer that protects data in transit

## Attack Surface (What to Inventory)

The full set of places where an attacker can interact with your system. Frequently-overlooked entries are at the bottom — the obvious ones are well-guarded; the forgotten ones are usually how attackers get in.

- **Endpoints** — every public API surface
- **Webhooks** — both inbound (you receive) and outbound (you call out)
- **Upload flows** — file uploads are a perennial attack vector (XSS in SVG, malware, zip bombs, type confusion)
- **Background jobs** — anything that processes data without a user in the loop
- **Message queues** — consumers that auto-process whatever lands on the queue
- **Third-party callbacks** — OAuth callbacks, payment processor webhooks, etc.
- **Old features nobody remembers** — the highest-leverage attacker target. Deprecated endpoints, sunsetted features, "legacy" auth paths

**The rule:** if it accepts input, it's part of the attack surface. The cataloging exercise is itself the value.

---

**Source:** https://newsletter.systemdesign.one/p/cybersecurity-fundamentals
**Source:** /Users/vimittal/Downloads/prep/prep.html (attack surface inventory)
**Date:** 2026-06-05, updated 2026-06-13
**Tags:** security, cia-triad, stride, threat-modeling, defense-in-depth, cybersecurity, fundamentals, index, attack-surface
