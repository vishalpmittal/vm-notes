# system-design/security — appsec, auth, crypto, cyber attacks

Application security, authentication/authorization, cryptographic primitives, and attack/defense patterns. This is the home for new security notes; the top-level security/owasp-top-10.md predates this folder.

## Notes
- [ai-agent-supply-chain.md](ai-agent-supply-chain.md) — third-party agent skills declaring one thing but doing another; BIV
- [api-security.md](api-security.md) — BOLA, scope/data minimization, encryption at every hop, API inventory
- [common-cyber-attacks.md](common-cyber-attacks.md) — six attack classes and the layered defenses that counter them
- [cybersecurity-fundamentals.md](cybersecurity-fundamentals.md) — CIA triad, STRIDE threat modeling, defense-in-depth, control categories
- [github-agentic-workflow-security.md](github-agentic-workflow-security.md) — three-layer defense-in-depth assuming AI agents get compromised
- [google-zanzibar.md](google-zanzibar.md) — relation-tuple authorization at trillions of records with zookies
- [hashing-encryption-tokenization.md](hashing-encryption-tokenization.md) — three primitives and the diagnostic question that picks one
- [jwt.md](jwt.md) — signed self-contained tokens, validation checks, and failure modes
- [mfa.md](mfa.md) — factor categories, hardware keys vs TOTP vs SMS, recovery-flow risk
- [oauth.md](oauth.md) — delegated authorization, access/refresh/ID tokens, client patterns
- [password-attacks.md](password-attacks.md) — guessing vs theft attack classes and layered defenses
- [password-storage-hashing.md](password-storage-hashing.md) — password KDFs, per-user salts, work factors, peppers
- [proof-of-human.md](proof-of-human.md) — five-pillar personhood verification with iris biometrics and nullifiers
- [sso.md](sso.md) — IdP-delegated auth, browser-mediated redirects, SAML vs OIDC
- [stripe-fraud-detection.md](stripe-fraud-detection.md) — real-time fraud scoring of 1,000+ signals via network visibility and DNNs
- [tls-ssl.md](tls-ssl.md) — handshake, key exchange, forward secrecy, certificate authorities
- [zero-trust-and-jit-access.md](zero-trust-and-jit-access.md) — assume-breach model and time-bounded privileged access
