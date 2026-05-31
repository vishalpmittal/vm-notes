# AI Agent Teams

## Key Takeaways

- Complex projects require teams of specialized subagents, not a single LLM -- analogous to human cross-functional teams where each member owns a distinct role
- Seven core subagent roles cover the full lifecycle: Doer, Planner, Tool Operator, Learner, Feedback/Critic, Supervisor, and Presenter
- The ReAct pattern (Action + Reasoning + Observation = Answer) maps directly onto these roles, making it a practical starting point for simpler agent designs
- Four optimization levers tune subagent performance: prompting, model selection, model tuning (fine-tuning), and context provision
- Agent teams transform a single engineer's output into a "highly capable unit" by addressing intellectual bottlenecks (architecture, validation, safety) rather than just accelerating code generation

## Why Agent Teams

A single LLM call breaks down on complex, multi-step projects. Building a mobile app involves input processing, requirements definition, architecture planning, coding, testing, and publication -- each step benefits from a different specialization. Agent teams mirror human organizations: each subagent contributes a unique role toward a unified output.

The article illustrates this with CDN-Folk, a Content Delivery Network built in 3.5 days using collaborative AI agents across a three-plane architecture (Automation, Control, Data).

## Seven Core Subagent Roles

| Role | Responsibility | CDN-Folk Example |
|------|---------------|------------------|
| **Doer** | Executes specific tasks (code, text generation) under strategic direction | Wrote Nginx/Varnish configuration files for the Data Plane |
| **Planner** | Decomposes complex problems into manageable steps | Structured the three-plane architecture (Automation, Control, Data) |
| **Tool Operator** | Interacts with external APIs, tools, and web services | Managed DNS providers and certificate systems integration |
| **Learner** | Gathers external information via RAG -- competitive analysis, market trends, technical guidance | Sourced latency optimization heuristics |
| **Feedback/Critic** | Reviews outputs, detects hallucinations, runs QA, validates configurations | Scored multiple options for selection before deployment |
| **Supervisor** | Oversees execution at micro (role-embedded) and macro (project) levels; prevents stalling | Coordinated distributed systems, identified failure points |
| **Presenter** | Communicates results to users; summarizes decisions and performance | Delivered architectural summaries and system performance reports |

## Mapping to ReAct

The ReAct pattern combines four elements that map onto the subagent roles:

- **Action** = Tool Operator (executes external calls)
- **Reasoning** = Planner (decomposes and sequences steps)
- **Observation** = Feedback/Critic (evaluates results)
- **Answer** = Presenter (communicates the final output)

ReAct is an "excellent starting point for simpler agent designs" -- the full seven-role team extends it for complex, multi-phase projects.

## Optimizing Subagents

Four levers for tuning subagent performance:

1. **Prompting** -- Clear, specific instructions that guide behavior and handle edge cases. The most accessible lever; iterate on prompts before reaching for heavier tools.
2. **Model Selection** -- Choose models based on specialization, reasoning capabilities, and personas. A Doer may use a fast code-generation model; a Planner may need a stronger reasoning model.
3. **Model Tuning** -- Fine-tune with positive/negative examples for resource-intensive or repetitive tasks. Higher upfront cost but better consistency at scale.
4. **Context Provision** -- Give each subagent strategic access to relevant systems and data without overwhelming its context window. Too little context causes hallucination; too much causes distraction.

## Engineering Impact

Agent teams shift the value proposition from "write code faster" to "address intellectual bottlenecks." The bottlenecks are architecture design, planning, validation, and safety modeling -- areas where a single engineer's cognitive bandwidth is the constraint. An agent team turns one engineer's output into a collective achievement by parallelizing specialized reasoning across these domains.

> See also: [Agentic Design Patterns](agentic-design-patterns.md) for the escalation ladder (direct API -> workflows -> agents -> multi-agent) and workflow/agent pattern taxonomy.

---

**Source:** https://aiagentssimplified.substack.com/p/ai-agent-teams
**Date:** 2026-04-22
**Tags:** multi-agent, agent-teams, subagent-roles, react-pattern, agent-optimization, cdn-folk
