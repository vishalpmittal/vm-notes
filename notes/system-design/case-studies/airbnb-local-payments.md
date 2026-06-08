# Airbnb's Local Payment Methods Platform

How Airbnb deployed 20+ local payment methods across 220+ countries in 14 months — by moving from a monolith to a config-driven, processor-agnostic platform that abstracts the messy reality of global payments.

## Key Takeaways

- The triggering business need: Airbnb's credit-card-only checkout **excluded millions** in markets with low card penetration. "Pay as a Local" added 20+ locally-preferred methods (Pix in Brazil, KakaoPay in Korea, etc.) in 14 months
- **Domain-driven decomposition** — broke the payments monolith into Pay-in (guest), Payout (host), Processing (PSP integration) subdomains
- **Multi-Step Transactions (MST)** — a processor-agnostic framework that normalizes diverse PSP flows into a common `ActionPayload` with status (e.g., `ACTION_REQUIRED`). The application doesn't need per-PSP branching
- **Config-driven integration** via YAML "Payment Method Config" — single source of truth that generates backend code. Reduced new-method launch from months to weeks
- A **Dynamic Payment Widget** with backend-driven UI configs renders country/currency-specific forms without client app updates — critical for mobile-app release cycles

## The Business Problem

Airbnb operates in 220+ countries. Credit cards aren't universal:

- **Brazil**: many users prefer Pix (instant bank transfer) or boleto (cash voucher)
- **South Korea**: KakaoPay, Naver Pay dominate
- **Germany / Netherlands**: SEPA direct debit, iDEAL
- **China**: Alipay, WeChat Pay
- **India**: UPI, wallets

A credit-card-only checkout is invisible-but-real exclusion. The "Pay as a Local" initiative deployed 20+ locally-preferred methods to fix it.

## Technical Architecture

### 1. Monolith → Domain-Driven Services

The legacy payments code was a monolith. The team split it into three subdomains:

| Subdomain | Responsibility |
|---|---|
| **Pay-in** | Guest payments — collecting money from guests |
| **Payout** | Host payments — disbursing money to hosts |
| **Processing** | PSP (payment service provider) integration — talking to Stripe, Adyen, local PSPs |

Decomposition isolates blast radius: adding a new local method in Brazil doesn't risk breaking host payouts.

### 2. Multi-Step Transactions (MST)

Many local payment methods need user actions outside Airbnb (Pix QR scan, bank redirect for SEPA, etc.). The naive integration: each PSP has its own flow, requiring per-PSP code branches.

The MST framework **normalizes diverse PSP flows into a common shape**:

- Vendors return `ActionPayload` objects with a status
- Status `ACTION_REQUIRED` triggers Airbnb to surface the next step to the user
- After user completes the action, the flow resumes through the same MST pipeline

Result: the Pay-in service doesn't have separate code paths for "Pix flow" vs "iDEAL flow" vs "Stripe 3DS challenge flow." All look like MST transitions.

### 3. Three Core Payment Flow Patterns

Every local payment method fits one of three patterns:

| Pattern | How | Example |
|---|---|---|
| **Redirect** | User completes payment externally, returns with confirmation token | iDEAL, bank-redirect methods |
| **Async** | User scans QR code; PSP webhooks notify Airbnb when paid | Pix, Alipay |
| **Direct** | Credentials entered in Airbnb's UI, real-time processing | Credit card |

These three cover ~all practical payment integrations.

### 4. Config-Driven Integration (YAML)

The biggest leverage point. A **Payment Method Config** in YAML is the single source of truth for each method:

- Display name and icon
- Supported currencies and countries
- Required fields and validation rules
- Flow type (Redirect / Async / Direct)
- PSP routing rules
- Webhook handler routing

Backend code for the new method is **auto-generated** from this config. The team doesn't write integration code per method — they write config.

**Impact:** new method launches went from **months → weeks**.

### 5. Dynamic Payment Widget

Backend-driven UI configuration:

- Mobile/web client requests "what does the payment form look like for this user in this country with this currency?"
- Backend returns a structured form description
- Client renders the form generically

**Why this matters:** mobile app release cycles are slow (App Store review, staged rollouts). Hardcoding country-specific form layouts in the client means every new method requires a client release. Backend-driven UI means add-config-deploy-backend-done, no client release needed.

### 6. In-House PSP Emulator + Centralized Observability

- **PSP Emulator** — simulates the messy scenarios (timeouts, partial failures, retries) that real PSPs produce. Lets the team test integration code reliably without staging environments at every PSP
- **Centralized monitoring** across four layers: client, backend, PSP, webhooks — standardized alerting rules across all 20+ methods

## Why Generalizable

The Airbnb stack maps to a generalizable playbook for any team integrating heterogeneous third parties at scale:

1. **Domain-decompose first.** Don't add complexity to a monolith
2. **Normalize external diversity** into a common internal abstraction (MST as an example)
3. **Config > code** for per-integration variation. Generate code from config
4. **Backend-driven UI** to avoid coupling integration cadence to client release cadence
5. **Build emulators** for external systems you can't reliably test against
6. **Unified observability** across all integrations from day one

## What This Doesn't Solve

- **Local regulatory compliance** — each country has its own KYC, AML, tax rules. The platform makes integration cheap; the legal work is separate
- **Funding/settlement logistics** — actual money movement requires banking partnerships per market
- **Currency hedging and FX** — converting local-method receipts into Airbnb's reporting currency is its own problem

## Related

- [Distributed system failure modes § fan-out](distributed-system-failure-modes.md#3-fan-out) — payment flows are classic fan-out (charge → inventory → email → notification → analytics)
- [Stripe fraud detection](../security/stripe-fraud-detection.md) — adjacent fintech case study on ML for risk scoring
- [Event-driven systems](event-driven.md) — payment async webhooks are the canonical event-driven use case
- [API retries](../api/retries.md) — idempotency-key requirement for payment APIs

---

**Source:** https://blog.bytebytego.com/p/how-airbnb-rolled-out-20-local-payment
**Date:** 2026-06-05
**Tags:** airbnb, payments, internationalization, multi-step-transactions, config-driven, payment-service-providers, fintech, case-study
