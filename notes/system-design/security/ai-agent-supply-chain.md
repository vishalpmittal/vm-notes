# AI Agent Supply Chain Security

Third-party "skills" for AI agents (executable code + YAML manifest + natural-language docs) run with privileged access once installed — and there is no automated verification that what a skill declares matches what it actually does. Unit 42's research on the [OpenClaw](../../ai-ml-ds/agents/openclaw-architecture.md) registry found 80% of skills exhibit at least one behavioral mismatch.

## Key Takeaways

- **80% of skills (39,933 / 49,943) in OpenClaw's registry** had at least one mismatch between declared and actual behavior — 250,706 total deviations
- Most deviations (81.1%) are developer oversight, but **18.9% are adversarial**; of those, ~60% are data theft / espionage
- **5% of skills (2,490) contain multi-stage attack chains** warranting mandatory security review; 88% of those chains are either silent credential exfiltration or instruction-override hijacking
- **Adversarial concentration varies wildly by capability** — 96% of instruction-manipulation deviations are adversarial vs. 10% of file-system ones. Flat-threshold review policies over-block routine I/O and under-review the dangerous categories
- The skill ecosystem mirrors mobile apps and browser extensions before automated auditing became standard — that's the gap **Behavioral Integrity Verification (BIV)** is designed to close

## Why Skills Are a Supply Chain Problem

A skill bundles three modalities that must be audited together:

| Modality | What it contains | How attackers abuse it |
|---|---|---|
| **Metadata** (YAML manifest) | Declared permissions, entry points | Under-declare what code actually does |
| **Executable code** (Python/JS/shell) | What runs at invocation | Hidden capabilities not surfaced in docs |
| **Natural-language instructions** (README, SKILL.md) | Prose describing behavior | Prompt-injection payloads that override the host agent |

Tens of thousands of these packages live in public registries with no automated check that the three modalities agree.

## Behavioral Integrity Verification (BIV)

BIV runs two parallel tracks against a fixed taxonomy of **29 capabilities in 7 families**: Network, File system, Process execution, Environment, Encoding, Credentials, Instruction-level threats.

### Declared Track

- Deterministic parsers for structured manifest fields
- LLM analysis for natural-language descriptions, with **every claim anchored to a quoted source span** (rejects fabrication)

### Actual Track

- AST-level taint analysis + regex + pattern matching across Python/JS/shell
- Separately scans NL instructions for prompt-injection and instruction-override patterns

### False-Positive Filters

- Reject outputs that echo the taxonomy verbatim
- Require capability claims to be anchored in quoted spans
- Demand domain-specific keywords in context for high-risk capabilities
- Every flagged deviation includes file-and-line evidence pointers

## Findings at Registry Scale

Scanned the entire OpenClaw registry (~49,943 skills, early 2026):

- **250,706 deviations** surfaced
- **80% of skills** had ≥1 mismatch

### Compound Threat Patterns

Four novel multi-stage chain categories the analyzer flags:

1. **Exfiltration chain** — `FILE_READ → base64 → NETWORK_SEND`
2. **Remote code execution chain** — `download → write → execute`
3. **Code obfuscation** — `encoding chain → dynamic eval`
4. **Data lineage violation** — `FILE_READ → FILE_WRITE` (mostly benign)

### Intent Breakdown (163,754 clustered deviations)

- **81.1% developer oversight** — doc errors, legit helper code, unused declarations, framework deps
- **18.9% adversarial intent**:
  - Data theft / espionage: ~60% of adversarial slice
  - Payload + infrastructure
  - Agent hijacking
  - Financial / destructive / social engineering: combined <1%

### Three Recommended Governance Tiers

| Tier | % of skills | Description | Action |
|---|---|---|---|
| Top | 5% (2,490) | Multi-stage attack chains | **Mandatory** security review |
| Middle | 16.8% | Single-stage adversarial deviations | Contextual review |
| Bottom | 72.5% | Benign | Documentation updates |

### Dominant Attack Patterns (88% of multi-stage chains)

1. **Silent credential exfiltration** — read secret → transmit
2. **Instruction-override hijacking** — take control of the decision loop → exfiltrate

## Where Risk Concentrates by Capability

Adversarial rate is **not uniform** — and this is the single most actionable finding:

| Capability family | Adversarial rate | Recommended severity |
|---|---|---|
| Instruction manipulation | 96% | Critical |
| Credentials | 56% | Critical |
| Network | 37% | High |
| Process execution | 12% | High |
| Environment | Moderate | High |
| File system | 10% | Medium |
| Encoding | Moderate | Medium |

A single threshold over-blocks I/O skills while waving through prompt-injection and credential-access skills. **Per-category review tiers keyed to severity** match the real risk distribution.

## Limitations

- BIV is **static analysis only** — dynamic dispatch and obfuscated payloads escape it
- Flagged skills are classifier-predicted candidates, not runtime-confirmed exploits
- LLM adjudicator is vulnerable to adversaries who calibrate descriptions after reading the research
- Backbone backdoors, retrieval-corpus poisoning, and runtime memory poisoning still need complementary runtime defenses (e.g., [GitHub's three-layer sandbox approach](github-agentic-workflow-security.md))

## Operational Recommendations

For any org running LLM agents with third-party skills in production:

1. **Inventory** all installed third-party skills
2. **Verify before install**, not after — integrate behavioral-integrity checks into the install path
3. **Apply per-category review severity** rather than a flat threshold
4. **Prioritize the two dominant patterns** (credential exfiltration, instruction-override hijacking) in incident response and policy

---

**Source:** https://unit42.paloaltonetworks.com/ai-agent-supply-chain-risks/
**Paper:** https://arxiv.org/abs/2605.11770
**Date:** 2026-06-12
**Tags:** ai-agents, supply-chain-security, mcp, openclaw, prompt-injection, credential-exfiltration, static-analysis, agent-skills, threat-modeling
