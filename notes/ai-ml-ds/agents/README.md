# ai-ml-ds/agents — single-agent concepts and real-world agent case studies

Notes on how one agent works (anatomy, memory, design patterns) plus production case studies of agents at real companies. Multi-agent teams and harness/loop engineering live in agent-teams-harness-eng/; Claude Code specifics live in claude/; model-serving lives in inference/.

## Notes

Grouped by topic. Files are stored flat in this folder — the grouping below is navigational.

### Foundations & concepts
How a single agent works, at three altitudes (primer → field map → pattern catalog), plus the memory model.
- [ai-agent-anatomy.md](ai-agent-anatomy.md) — the six components and the perceive-plan-act loop of an agent
- [agentic-engineering-overview.md](agentic-engineering-overview.md) — field map of 30 agentic-engineering concepts across 6 layers
- [agentic-design-patterns.md](agentic-design-patterns.md) — escalation ladder and the 9 workflow/agent patterns
- [agent-memory-state-consistency.md](agent-memory-state-consistency.md) — state vs memory, three memory tiers, and how agent memory fails

### Production & reliability
Making agents dependable at scale — architecture, the demo-to-production gap, and enterprise operating discipline.
- [production-agent-architecture.md](production-agent-architecture.md) — propose-and-validate backend, eight operational layers, router/worker/critic
- [ai-agents-overhyped.md](ai-agents-overhyped.md) — demo-to-production reliability gap; AI as operating model
- [salesforce-agentforce-lessons.md](salesforce-agentforce-lessons.md) — lessons from 20,000 enterprise agent deployments

### Coding agents
Agents that write and ship software, and where they help vs. hurt across the lifecycle.
- [coding-agents-overview.md](coding-agents-overview.md) — index/comparison of the repo's coding-agent notes
- [cursor-coding-agent.md](cursor-coding-agent.md) — Cursor's Composer MoE model and production coding architecture
- [agents-across-sdlc.md](agents-across-sdlc.md) — where agents win vs lose across the software lifecycle

### Company case studies
Real agent systems shipped at real companies.
- [openai-data-agent.md](openai-data-agent.md) — OpenAI's internal data agent; context assembly, fewer tools, and engineering for trust (memory governance, outcome-based eval, inherited auth)
- [grab-ai-agents-engineering-productivity.md](grab-ai-agents-engineering-productivity.md) — Grab's multi-agent system over a 15,000-table warehouse
- [ai-customer-support-at-scale.md](ai-customer-support-at-scale.md) — resolve-or-escalate design across three travel platforms
- [agents-for-infrastructure-design.md](agents-for-infrastructure-design.md) — agents as architecture collaborators (3.5-day CDN build)
- [openclaw-architecture.md](openclaw-architecture.md) — always-on daemon bridging LLM reasoning to real-world execution

### Applications
Applying agents to specific knowledge-work problems.
- [product-validation-with-agents.md](product-validation-with-agents.md) — specialized agents for market/customer research and build decisions
- [trusting-ai-generated-visuals.md](trusting-ai-generated-visuals.md) — orchestration layer to keep humans in control of AI-generated images

### Recurring cross-note themes
The same lessons surface across multiple notes above — convergent evidence, not duplication:
- **Fewer, non-overlapping tools beat many tools** — [openai-data-agent](openai-data-agent.md), [grab-…](grab-ai-agents-engineering-productivity.md), [production-agent-architecture](production-agent-architecture.md)
- **The harness/infra matters as much as the model** — [coding-agents-overview](coding-agents-overview.md), [production-agent-architecture](production-agent-architecture.md), [openai-data-agent](openai-data-agent.md)
- **Propose → validate; human-in-the-loop for writes** — [production-agent-architecture](production-agent-architecture.md), [grab-…](grab-ai-agents-engineering-productivity.md), [ai-customer-support-at-scale](ai-customer-support-at-scale.md), [salesforce-…](salesforce-agentforce-lessons.md)
- **The demo→production reliability gap** — [ai-agents-overhyped](ai-agents-overhyped.md), [salesforce-…](salesforce-agentforce-lessons.md), [production-agent-architecture](production-agent-architecture.md)
