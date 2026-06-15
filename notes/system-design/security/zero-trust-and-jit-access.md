# Zero Trust and Just-In-Time Privileged Access

The shift from "trusted internal network" to **assume breach** — every request authenticated, every privilege time-bounded, every action audited.

## Key Takeaways

- **Zero Trust assumes no implicit trust** based on network, device, or location. Every request authenticates, authorizes, and is continuously re-validated — the "perimeter" is gone
- **Standing admin accounts violate Zero Trust by design** — they're long-lived, broadly-scoped, and rarely rotated. JIT privileged access fixes this
- **JIT (Just-In-Time)** privilege means access is **elevated only when needed**, for a **limited time**, with **approvals + session monitoring**. Eliminates the "always-admin" problem
- The goal is **shrinking the blast radius** of a compromised credential — a 1-hour SSH token is dramatically less dangerous than a permanent admin account
- Zero Trust + JIT is also a **compliance + audit posture** improvement, not just a security one — every privileged action has a timestamped, approved, scoped log

## Zero Trust Principles

> "Zero Trust assumes no implicit trust based on network, device, or location. Every request must be authenticated, authorized, and continuously validated."

The principles:

- **No "trusted internal network"** — internal traffic gets the same scrutiny as external
- **Continuous identity verification** — re-check authentication and authorization throughout a session, not just at the door
- **Context-aware authorization** — device posture, location, time of day, behavior signals all feed the policy decision
- **Least-privilege access** — minimum permissions to do the job, granted only when needed
- **Short-lived tokens** — minutes-to-hours, not weeks-to-months
- **Strong auditability** — every access decision is logged with context for post-hoc investigation

## Just-In-Time (JIT) Privileged Access

The corollary of Zero Trust for *admin* operations.

> "JIT privilege means users don't hold permanent admin rights. Access is elevated only when needed, for a limited time, and often with approvals and session monitoring."

### Why standing admin is dangerous

- **Always-on attack surface** — a compromised admin account is immediately destructive
- **Hard to audit** — when admin = normal state, you can't distinguish legitimate from malicious use
- **Hard to revoke** — if access never expires, it never gets pruned

### How JIT works

- **No permanent admin role** — users have read-only or low-privilege by default
- **Request → approve → time-bounded elevation** — typically minutes to hours
- **Session recorded + audited** — full keystroke / command log for the elevated window
- **Auto-revocation** at expiry — no manual cleanup

### Example

> A support engineer gets **1-hour temporary SSH access** to a production server. Actions are logged, keys auto-revoked at the 1-hour mark. No cleanup needed; no "I'll remove their access later" todo.

## Why It Matters Operationally

- **Eliminates standing admin accounts** — the highest-value targets
- **Reduces blast radius** if credentials are compromised (1 hour of damage vs unlimited)
- **Improves compliance + audit posture** — clean, scoped, time-bounded access logs map directly to SOC2 / ISO27001 / HIPAA evidence
- **Forces explicit access decisions** — the approval gate creates a natural audit trail and a chance to question "why does X need root right now?"

## See Also

- [mfa.md](mfa.md) — MFA is a Zero Trust prerequisite — strong identity is the foundation
- [oauth.md](oauth.md), [jwt.md](jwt.md) — short-lived tokens are the core mechanism
- [google-zanzibar.md](google-zanzibar.md) — fine-grained authorization model that pairs well with Zero Trust
- [cybersecurity-fundamentals.md](cybersecurity-fundamentals.md) — defense-in-depth context

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** zero-trust, jit-access, privileged-access-management, pam, least-privilege, assume-breach, session-monitoring, compliance
