# Production Agent Architecture

## Key Takeaways

- The LLM is the **reasoning engine**; the backend is the **reality engine** — a more capable model cannot compensate for an undisciplined backend
- The golden rule: **Propose and Validate** — the model proposes actions, the backend validates, only approved actions execute. The model must never directly mutate business state
- Eight operational layers sit between user and execution: identity → context builder → tool registry → policy engine → state validator → execution → feedback loop → observability
- **Context is not a data dump** — the context builder curates the minimum relevant, secure slice of data for each task; shoving everything in generates expensive, complex errors
- **The semantic repair loop** converts backend failures into structured feedback so the agent can self-correct rather than silently failing or hallucinating a fix
- Separate low-risk operations (draft, summarize) from high-risk mutations (delete, bill) — human approval for the latter builds organizational trust incrementally

![The Reality Engine — backend architecture for production AI agents](../../images/20260618-1600-propose-validate-pipeline.jpeg)

## Reasoning Engine vs. Reality Engine

The common mistake: treating a more capable model as the solution to production failures. A stronger model inside an undisciplined context window generates more expensive, more complex errors.

The split:
- **Reasoning Engine (LLM)** — plans, summarizes, and makes proposals
- **Reality Engine (backend)** — validates proposals, controls execution, enforces policy

The backend is not a passive relay. It enforces the rules the model cannot be trusted to self-enforce.

## The Propose and Validate Pattern

The execution pipeline decouples tool calling from direct execution:

```
Model → Proposed Action → Validator → Executor → Result → Feedback → Response
```

**Example (CRM agent following up with demo contacts):**
1. Model proposes: "Send follow-up email to demo contacts from last week"
2. Validator audits: check ownership, opt-out status, user authorization
3. Executor sends only if all checks pass
4. Result is translated into model-readable feedback

The model never writes to a database, sends an email, or calls an API directly. Every mutation goes through the validation pipeline.

## Eight Operational Layers

| Layer | Role |
|---|---|
| **1. Identity** | Authenticate users, establish workspace boundaries, enforce RBAC, limit data visibility |
| **2. Context Builder** | Curate selective, relevant, secure information for the model's reasoning window |
| **3. Tool Registry** | Maintain valid schemas, input expectations, descriptions, pre-calculated risk profiles |
| **4. Policy & Permission Engine** | Segregate operations by risk level — determine what runs autonomously vs. requires approval |
| **5. State Validator** | Audit proposals against current business data for compliance before execution |
| **6. Execution Layer** | Heavily logged, predictable system communicating with APIs and databases |
| **7. Feedback Loop** | Translate execution outcomes into model-readable inputs for re-planning |
| **8. Observability** | End-to-end traces: prompts, injected context, model decisions, validation logs |

## Memory Architecture

Agent memory is not a cache or an infinite store. Structure:

- **Source of truth**: relational database — immutable, authoritative
- **Agent working memory**: short-term task context, cleared on completion
- **High-level summaries**: preserved across turns for continuity

Agents should query backends explicitly for static facts (permissions, invoice status, account state) rather than assuming the model retains them. Context windows hold reasoning state, not data.

## Semantic Repair Loop

When backends safely block an invalid action, the failure is not the end — it is input for the next iteration:

1. Execution fails (invalid operation, policy violation, data conflict)
2. Failure is translated into structured semantic feedback
3. Model receives explanation, dynamically adjusts parameters or requests clarification
4. Loop continues until resolved or escalated

This replaces silent failures (agent gives up) and hallucinated fixes (agent pretends it succeeded).

## Human Approval Workflows

Separate the autonomy spectrum by risk:

| Risk Level | Examples | Handling |
|---|---|---|
| Low | Draft, summarize, read | Fully autonomous |
| Medium | Update, notify, schedule | Autonomous with logging |
| High | Delete, billing mutation, external send | Human approval required |

Incrementally expanding autonomy as trust builds is safer than starting with full autonomy and adding restrictions after incidents.

---

**Source:** https://aiagentssimplified.substack.com/p/rule-1-of-production-ready-agents
**Date:** 2026-06-18
**Tags:** agents, production, validation, backend-architecture, tool-use, safety, observability
