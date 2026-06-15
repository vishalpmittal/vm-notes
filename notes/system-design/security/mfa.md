# Multi-Factor Authentication (MFA)

Requires **more than one form of verification** before granting access. The simplest, highest-ROI security control most systems still don't enable everywhere.

## Key Takeaways

- MFA combines factors from three categories: **something you know** (password), **something you have** (phone/key), **something you are** (biometric). Two from different categories = MFA
- **Hardware keys (FIDO2/U2F) are the strongest** — phishing-resistant by design. App-based TOTP is next best. **SMS OTP is the weakest** because of SIM-swap attacks
- MFA stops the majority of credential-theft attacks even when the password is leaked — it's the highest-leverage single control most accounts can enable
- The real-world failures are usually **recovery flows**, not the MFA itself — a phone-loss recovery path that bypasses MFA is the actual attack surface
- "MFA fatigue" attacks (spamming push notifications until the user accepts) are now common — number-matching and rate-limiting fix this

## The Three Factor Categories

| Category | Examples |
|---|---|
| **Something you know** | Password, PIN, security questions |
| **Something you have** | Phone (SMS, TOTP app, push), hardware token (YubiKey), smart card |
| **Something you are** | Fingerprint, facial recognition, iris scan |

Two from *different* categories = real MFA. Password + security question is *not* MFA — both are "something you know."

## Methods Ranked

| Method | Factor Type | Security Level | Notes |
|---|---|---|---|
| **Hardware keys (U2F/FIDO2)** | Have | **Very High** | Phishing-resistant by protocol |
| **Authenticator apps (TOTP)** | Have | High | Codes rotate every 30–60s |
| **Push-based MFA** (Duo, Okta) | Have | High | Approve via mobile app; vulnerable to MFA-fatigue without number-matching |
| **Biometric** (fingerprint, face) | Are | High | Convenient, device-specific |
| **SMS OTP** | Have | **Medium** | Vulnerable to SIM swapping — avoid for high-value accounts |
| **Email OTP** | Have | Medium | Slower; only as strong as the email account itself |

## Pros and Cons

### Pros
- **Stronger security**: stolen password isn't enough
- **Compliance**: meets HIPAA, GDPR, PCI-DSS requirements
- **Reduces phishing**: hardware tokens / FIDO2 are phishing-resistant
- **Protects critical systems**: cloud accounts, VPNs, financial accounts, admin dashboards

### Cons / Limitations
- **User friction**: extra step annoys users
- **Recovery complexity**: lost device can lock out legitimate users — the recovery path becomes the new attack surface
- **Cost**: hardware tokens add per-user cost at scale
- **Partial coverage**: SMS OTP is vulnerable; not all MFA is equal

## Best Practices

- **Prefer app-based or hardware MFA over SMS** — SIM swap is a real, common attack
- **Enable MFA on all critical accounts** — email (because it's the password-reset root of trust), banking, enterprise logins, cloud admin
- **Have a recovery plan** — backup codes printed and safely stored, secondary device, or admin-mediated recovery
- **Use number-matching for push MFA** to defeat fatigue attacks (Microsoft, Okta, Duo now default to this)
- **Combine factors wisely** for sensitive systems — password + hardware key + optional biometric on the device
- **Monitor and audit** — track login patterns, MFA bypass attempts, and unusual device registrations
- **Educate users** — "MFA is security, not annoyance" — explain *why* before rolling out, not after complaints

## The Real Attack Surface Is Recovery

The most common real-world MFA bypass isn't breaking the MFA — it's the recovery flow. A support agent who can reset MFA after a phone call, a "forgot device" link that emails a temporary code, or a poorly-secured backup code stash — these are how MFA actually gets circumvented.

**Design the recovery path with the same threat model as the primary path.**

## See Also

- [sso.md](sso.md) — SAML/OAuth/OIDC; MFA usually plugs in at the IdP
- [oauth.md](oauth.md) — OAuth flows where MFA happens during user auth
- [jwt.md](jwt.md) — claims sometimes encode MFA assurance levels
- [zero-trust-and-jit-access.md](zero-trust-and-jit-access.md) — MFA is a foundational primitive for zero-trust
- [cybersecurity-fundamentals.md](cybersecurity-fundamentals.md) — defense-in-depth

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** mfa, 2fa, authentication, totp, fido2, yubikey, sim-swap, mfa-fatigue, push-based-auth
