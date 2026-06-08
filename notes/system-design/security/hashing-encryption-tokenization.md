# Hashing vs Encryption vs Tokenization

Three security primitives that get conflated, with one diagnostic question that picks the right one.

## Key Takeaways

- **The diagnostic question: "Do you need to recover the original value?"** No → hashing. Yes for confidentiality → encryption. Yes but to limit blast radius → tokenization
- **Hashing** is one-way (verify integrity, store passwords). Use bcrypt/Argon2 for passwords, never general-purpose hashes (MD5, SHA-256) — see [password-storage-hashing](password-storage-hashing.md)
- **Encryption** is reversible with a key (protect data in transit/at rest). Requires robust key management. Does *not* limit how widely data spreads
- **Tokenization** replaces sensitive data with meaningless surrogates that only have meaning inside specific systems. Contains exposure rather than hiding values. Common in PCI-DSS payment systems
- Mismatching the problem to the tool creates **silent vulnerabilities** — using encryption where you needed hashing means you have a reversible password store

## The Three Tools at a Glance

| | Hashing | Encryption | Tokenization |
|---|---|---|---|
| **Reversible?** | No | Yes (with key) | Yes (with vault) |
| **Primary goal** | Integrity, authenticity | Confidentiality | Containment |
| **Output relationship to input** | Deterministic, fixed-length digest | Ciphertext same-ish size, requires key to decrypt | Random surrogate, no math relationship |
| **Use cases** | Passwords, file integrity, digital signatures | TLS, encrypted-at-rest data, secure messaging | Payment systems (card → token), compliance |
| **Key management?** | No keys | Critical — keys must be managed | Vault-based or vaultless |
| **Spread limitation** | None | None | Yes — token has no meaning outside |
| **Algorithm examples** | bcrypt, Argon2, SHA-256 | AES-256-GCM, ChaCha20, RSA | Format-preserving tokens, random IDs |

## When to Use Each

### Hashing
- Storing passwords (use [Argon2id, bcrypt — never MD5/SHA-256 alone](password-storage-hashing.md))
- File integrity checks (download verification, content-addressable storage)
- Digital signatures (sign the hash, not the document)
- Deduplication (same content → same hash → reuse storage)

**Never use hashing when** you need to retrieve the original value. You can't.

### Encryption
- Data in transit — TLS encrypts the wire
- Data at rest — disk encryption, encrypted database columns
- Secure messaging — Signal-style E2EE
- Backups and archives

**Never use encryption when** you never need retrieval (use hashing) or want to limit data spread (use tokenization). Encryption protects values; it doesn't stop them from being copied around.

### Tokenization
- Payment card data (PCI-DSS scope reduction)
- Health records / PII in compliance-sensitive environments
- Any case where the secret needs to flow through systems that **don't need to know its actual value**

**How tokenization differs from encryption:**
- An encrypted card number is still a card number to anyone with the key
- A tokenized card number is meaningless to every system except the vault. Even if a downstream system is compromised, the attacker has tokens — useless without vault access

**Vault-based vs vaultless:**
- Vault-based: a central system holds the mapping `token ↔ real value`
- Vaultless: tokens are derived (often format-preserving encryption) without a central lookup table — useful when central vault would be a SPOF

**Never use tokenization when** you have no compliance need or you need to query/analyze the original values. Tokenization breaks `SELECT WHERE card_number = ...` patterns; you'd be querying tokens.

## The Cheat Sheet

```
Need original value back?
  ├─ No
  │   └─ Hashing   (use Argon2/bcrypt for passwords)
  │
  └─ Yes
      ├─ Just want to keep it secret in transit/at rest
      │   └─ Encryption   (manage keys properly)
      │
      └─ Want to limit blast radius — keep secret value out of many systems
          └─ Tokenization   (vault-based or vaultless)
```

## Common Mismatches in the Wild

1. **Encrypting passwords** instead of hashing. Now you have a reversible password store — if the keys leak, all passwords leak. (Adobe 2013 breach.) Use hashing.
2. **Hashing PII to "anonymize"** it. If the input space is small (email addresses, phone numbers), an attacker can brute-force the hash. Use tokenization or proper anonymization.
3. **Not tokenizing card data** in non-payment microservices. Now every service in the call chain is in PCI scope. Use tokenization at the edge.
4. **Encrypting once and never rotating keys**. Encryption with stale keys is encryption without key management.

## Related

- [Password storage and hashing](password-storage-hashing.md) — hashing applied to passwords specifically (KDFs, salts, peppers)
- [Common cyber attacks](common-cyber-attacks.md) — MitM is the attack encryption defends against
- [OAuth](oauth.md) — tokens in auth context (different "token" — auth tokens vs tokenization tokens)
- [SSO](sso.md) — encrypted/signed assertions between IdP and SP

---

**Source:** https://blog.levelupcoding.com/p/hashing-vs-encryption-vs-tokenization
**Date:** 2026-06-05
**Tags:** hashing, encryption, tokenization, security, cryptography, pci-dss, kdf, vault, data-protection
