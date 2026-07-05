# Proof of Human: Verifying a Person in the Age of AI

## Key Takeaways
- Five pillars: Uniqueness (iris biometric), Anonymity (AMPC — secret sharing across jurisdictions), Recovery (public key registry + authenticators), Verification (ZK-proof nullifiers), Delegation (AgentBook per-human quotas for AI agents)
- The Orb processes iris scans locally — the original image is deleted before any data leaves the device, transmitting only a derived representation
- Duplicate detection happens in "abstract space" — the system can detect a returning iris without storing or comparing the raw biometric
- Anonymity is enforced architecturally: no single organization holds enough data to reconstruct an identity; three independent orgs in different jurisdictions each hold one share
- Nullifiers solve the one-person-one-action problem without linking actions: same (credential + relying party + action) combination always produces the same nullifier; different combinations produce uncorrelated numbers

![Proof of Human system overview — five pillars, Orb hardware, AMPC nodes, ZK verification, agent delegation](../images/20260705-1000-proof-of-human-overview.png)

## The Problem

Every major online platform is built on the assumption that accounts represent distinct humans. AI agents can now create unlimited accounts, pass CAPTCHAs, and mimic human behavior at scale. The core question: how do you prove a unique human is behind an account without collecting or centralizing identity data?

## Five Pillars

### 1. Uniqueness

**Goal:** Ensure each person can only register once.

**How:** The Orb — a dedicated hardware device — captures a high-resolution iris scan using multispectral imaging and liveness detection (to reject photos or replicas). Processing happens entirely on-device. The original image is deleted before transmission; only a derived iris code leaves the device.

**Duplicate detection in abstract space:** The system maps iris codes into a mathematical space where similar irises land close together. Duplicate detection compares positions in this space — not raw biometrics — so the original data is never reconstructed. False match rate: 1 in 100 billion.

**Current limitation:** The Orb is centralized hardware. Decentralizing the verification device is an open problem.

### 2. Anonymity

**Goal:** Prevent the verification system from becoming a surveillance system.

**How:** AMPC — Anonymized Multi-Party Computation. When an iris code is processed, it is split into three statistically random pieces (secret shares) using a threshold scheme. Each piece goes to an independent organization in a different jurisdiction. No single party holds enough to reconstruct the iris or identify the person.

**SMPC Nodes** execute the multi-party computation for duplicate detection without any party seeing the full data.

**OPRF Nodes** (Oblivious Pseudorandom Functions) participate in nullifier generation — they process inputs without learning what they're processing.

### 3. Recovery

**Goal:** Let people re-verify if they lose access without re-collecting biometrics.

**Components:**
- **WorldIDRegistry** — public on-chain registry that stores authorized public keys (not biometrics)
- **Authenticators** — software (mobile app) or hardware (YubiKey-style) devices that hold the corresponding private keys
- **Recovery agents** — trusted parties (friends, institutions) who can attest identity and authorize registration of a new key

**Recovery flow:** If an authenticator is lost, a recovery agent re-verifies the person and registers a new public key in the registry. No biometric re-collection required.

### 4. Verification

**Goal:** Prove unique humanity to third-party services without revealing who you are or linking separate actions.

**Mechanism — Nullifiers:**

A nullifier is a number derived from three inputs:
- The person's credential (from their iris registration)
- The relying party's ID (the service requesting proof)
- The action being taken

Properties:
- Same combination of all three → same nullifier every time (enables deduplication)
- Different combination → uncorrelated number (prevents cross-service linking)
- One-way: cannot reverse a nullifier to recover the credential

**ZK Proofs:** The user generates a zero-knowledge proof that they have a valid credential and computes the correct nullifier — without revealing the credential itself. Implemented via the IDKIT SDK.

**Oblivious Nullifier Pool:** Prevents nullifier reuse without the system learning which nullifier corresponds to which credential.

### 5. Delegation

**Goal:** Extend human-verified trust to AI agents acting on a person's behalf.

**How:** AgentBook — a registry that ties AI agent credentials to a human's WorldID. Each human can authorize a limited set of agents. Per-human quota: default 3 uses per service.

**AgentKit SDK** handles the agent-side credential presentation.

**Open question:** If AI agents consume per-human quotas, economic incentives may push humans toward delegating excessively, or toward creating fake delegations — the bootstrap problem.

## Technical Infrastructure

| Component | Role |
|---|---|
| Orb | Local iris capture + processing hardware |
| WorldIDRegistry | On-chain public key registry |
| SMPC Nodes | Multi-party computation for anonymous duplicate detection |
| OPRF Nodes | Oblivious pseudorandom function; participates in nullifier generation |
| IDKIT SDK | Developer SDK for ZK proof generation and verification |
| AgentBook | Registry linking AI agent credentials to human WorldIDs |
| AgentKit SDK | Agent-side credential presentation |

## Open Questions

| Challenge | Why it matters |
|---|---|
| Orb decentralization | Single hardware manufacturer = trust bottleneck; open hardware is the goal |
| Cryptographic resilience | Current ZK schemes may not be quantum-resistant |
| Agent economics | Per-human quotas may be too restrictive or too generous; market will test this |
| Bootstrap problem | Getting people to register before services require it; chicken-and-egg |

---

**Source:** https://blog.bytebytego.com/p/proof-of-human-how-to-verify-a-person
**Date:** 2026-06-30
**Tags:** identity, proof-of-human, world-id, biometrics, zero-knowledge-proofs, mpc, anonymity, digital-identity, ai-agents, nullifiers, orb, smpc, oprf, worldcoin, zkp, privacy, decentralized-identity
