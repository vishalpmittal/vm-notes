# AI Security Platform

A taxonomy for thinking about enterprise AI security — the risks, the business outcomes when those risks land, and the platform capabilities that try to contain them.

## Key Takeaways

- AI security is **not just prompt injection** — it spans model artifacts, runtime behavior, posture, and agent identity
- The business-level outcomes (IP theft, financial loss, brand damage, compliance fines, slowed innovation) are how AI security gets funded; framing technical risks in those terms is the conversation that gets budget
- Modern AI security platforms cluster into five capability areas; most vendors cover a subset, not all
- Agent-era threats (identity impersonation, memory manipulation, tool misuse) are distinct from model-era threats (prompt injection, hallucination, leak) and need their own controls

## Top Threats

- **Prompt Injection** — adversarial input that overrides intended instructions (direct or indirect, via tool/RAG content). See [prompt-injection-defenses.md](prompt-injection-defenses.md) for technical defenses
- **Insecure Outputs (Hallucinations)** — model produces confident-sounding but wrong content; downstream systems treat it as authoritative
- **Data Leaks** — sensitive data ingested via prompts, embeddings, or fine-tuning data ends up exposed in outputs or logs

## Business Outcomes (Why Leadership Cares)

| Outcome | What it looks like |
|---|---|
| **Compromised confidentiality + IP theft** | Model leaks proprietary code, customer data, internal strategy |
| **Operational chaos + financial bleed** | Agent takes unintended actions; bad outputs drive bad decisions; remediation cost compounds |
| **Eroded customer trust + brand damage** | Public hallucination, harmful output, or visible breach |
| **Compliance failures + fines** | GDPR / HIPAA / SOC 2 / sector-specific (GLBA, FERPA) violations from improper data handling |
| **Stifled innovation + competitive disadvantage** | Over-restrictive policy in response to fear, blocking adoption entirely |

The last one is often missed — under-investing in AI security creates the conditions for an over-correcting ban that costs more than the breach would have.

## AI Security Platform — Capability Areas

Enterprise platforms cluster around five areas. Map your stack against this list to find your gaps.

### 1. Model Scanning

Inspect model artifacts before deployment:
- Provenance and supply chain checks on downloaded weights
- Embedded backdoor / trojan detection
- License and policy conformance
- Vulnerability scanning of accompanying code/dependencies

### 2. Posture Management

Continuous visibility into AI assets across the org:
- Inventory of all models in production (often hundreds of shadow models)
- Configuration drift detection
- Data flow mapping (what data feeds which model)
- Access policy auditing

### 3. AI Red Teaming

Adversarial testing as a practice, not a one-off:
- Automated attack libraries (jailbreaks, injection payloads, extraction attempts)
- Continuous evaluation against new attack patterns
- Human red teams for the failure modes that don't automate well
- See [red-teaming-strategy.md](../../leadership/red-teaming-strategy.md) for the broader leadership lens

### 4. Runtime Security

Inline controls on live model traffic:
- Prompt injection detection on inputs
- Toxic / sensitive content blocking on outputs
- Data leak prevention (PII, secrets, internal identifiers in outputs)
- Resource overload / abuse rate limiting
- Hallucination detection (factuality / grounding checks)

### 5. AI Agent Security

The new surface that agents created:
- **Identity impersonation** — preventing agents from claiming to be a different principal. See [agent-identity-and-auth.md](agent-identity-and-auth.md)
- **Memory manipulation** — defending against poisoned long-term memory writes
- **Tool misuse** — bounding what agents can do with which tools, in which contexts (least privilege, human-in-the-loop on high-stakes actions)

## How to Use This Taxonomy

- **Audit:** for each area, write down what you have (or don't). Empty cells are the conversation
- **Prioritize:** model scanning and posture management are foundational; agent security is the fastest-moving frontier
- **Buy vs. build:** vendor platforms cover the broad surface fast; building gives precision but the threat landscape moves faster than most internal teams can keep up with
- **Don't over-index on prompt injection.** It gets all the press, but data leaks and agent tool misuse are typically higher-impact in production

## Related

- [Prompt injection defenses](prompt-injection-defenses.md) — technical defense techniques (runtime security area)
- [Agent identity and auth](agent-identity-and-auth.md) — the foundation under agent security
- [GitHub agentic workflow security](../../system-design/security/github-agentic-workflow-security.md) — concrete agent-tool-misuse case
- [Cybersecurity fundamentals](../../system-design/security/cybersecurity-fundamentals.md) — broader security taxonomy AI security extends

---

**Source:** /Users/vimittal/Downloads/prep/prep.html (Security section)
**Date:** 2026-06-17
**Tags:** ai-security, prompt-injection, hallucination, data-leak, model-scanning, posture-management, ai-red-teaming, runtime-security, ai-agent-security, enterprise-ai, compliance, taxonomy
