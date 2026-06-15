# TLS / SSL (HTTPS)

How secure communication over the internet actually works — the handshake, key exchange, and why it's both fast *and* secure. **TLS is the modern version of SSL**; people still say "SSL certificate" but it's almost always TLS now.

## Key Takeaways

- TLS provides three properties: **encryption** (no eavesdropping), **integrity** (no tampering without detection), and **authentication** (the server is who it claims)
- The handshake uses **asymmetric encryption** (RSA or ECDHE) to securely exchange a **session key**, then switches to **symmetric encryption** (AES, ChaCha20) for the actual data — fast *and* secure
- **Diffie-Hellman / ECDHE** is the modern key-exchange default because it gives **perfect forward secrecy** — even if a private key leaks years later, past traffic can't be decrypted
- A digital certificate signed by a trusted **Certificate Authority (CA)** is what makes "the server is who it claims" verifiable
- Session keys are short-lived for the same forward-secrecy reason — compromise of a single session doesn't compromise the others

## What TLS Provides

| Property | What it means |
|---|---|
| **Encryption** | Nobody can eavesdrop on data in transit |
| **Integrity** | Data can't be tampered with without detection (HMAC) |
| **Authentication** | Verifies the server (and optionally the client) is who it claims to be |

## The Handshake (5 Steps)

When you open `https://...`, this happens *before* any HTTP request:

### Step 1 — Client Hello
Browser → Server. Sends:
- Supported TLS versions
- Supported cipher suites (encryption algorithms)
- A random number for key generation

### Step 2 — Server Hello
Server → Browser. Sends:
- Selected TLS version and cipher suite
- Digital certificate (issued by a Certificate Authority)
  - Contains the server's public key
  - Confirms server identity
- A random number for key generation

### Step 3 — Certificate Verification (client-side)
Client checks the server certificate:
- Signed by a trusted CA?
- Not expired?
- Matches the server domain?
- If invalid → browser warns about insecure connection

### Step 4 — Key Exchange
Both sides agree on a shared **session key** for symmetric encryption. Two common methods:

- **RSA key exchange** — client encrypts a random session key with the server's public key; server decrypts with its private key
- **Diffie-Hellman / ECDHE** — both sides generate the shared key from exchanged public values without either ever transmitting the key itself. Gives **perfect forward secrecy** — even if the server's long-term private key is later compromised, old session keys can't be derived

ECDHE is the modern default. RSA key exchange is being phased out specifically because it lacks forward secrecy.

### Step 5 — Finished
- Both client and server have the same session key
- Each sends an encrypted "finished" message proving they got the same key
- Handshake complete → all subsequent traffic encrypted

## Data Transmission

After the handshake, all HTTP requests and responses use **symmetric encryption** with the session key:

- **AES** (Advanced Encryption Standard) or **ChaCha20** — fast block/stream ciphers
- **HMAC** for tamper detection
- The session key is short-lived; new connections get new keys

## Why TLS Is Secure

| Mechanism | What it protects |
|---|---|
| Asymmetric encryption for handshake | Secure key exchange over insecure networks |
| Symmetric encryption for data | Fast, efficient encryption of large traffic |
| Digital certificates | Authentication of the server (and optionally client) |
| HMAC over every record | Tamper detection |
| Short-lived session keys + forward secrecy | Past traffic stays safe even if long-term keys leak |

## Common Misconceptions

- **"HTTPS uses asymmetric encryption for everything"** — wrong; only the handshake. Asymmetric is too slow for bulk data
- **"A self-signed certificate is insecure"** — wrong; the encryption is identical. It just can't be *authenticated* without a trusted CA
- **"TLS = certificate"** — the certificate is one piece. Cipher suite negotiation, key exchange, and HMAC are equally critical
- **"Sites without HTTPS are fine if they don't have logins"** — wrong; ISPs and other intermediaries can inject content, track behavior, or strip security headers on any unencrypted connection

## See Also

- [hashing-encryption-tokenization.md](hashing-encryption-tokenization.md) — the building blocks (hash, sym/asym encryption, tokenization)
- [cybersecurity-fundamentals.md](cybersecurity-fundamentals.md) — CIA triad, STRIDE, defense-in-depth
- [oauth.md](oauth.md) — uses TLS as a prerequisite for token security

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** tls, ssl, https, encryption, certificates, ecdhe, perfect-forward-secrecy, handshake, key-exchange
