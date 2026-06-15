# The Anatomy of an AI Agent

## Key Takeaways

- An AI agent is a while-loop around an LLM — perceive, plan, act, observe, repeat until done or budget exhausted
- Six components define an agent: brain (LLM), planning, tools, memory, the loop, and guardrails
- Memory splits into short-term (context window) and long-term (vector stores, knowledge bases); when the window fills, agents summarize old turns and carry the summary forward
- Tools turn LLMs from text generators into autonomous actors — the model requests a tool, the runtime executes it, the result feeds back into the loop
- Guardrails scale with autonomy: sandboxing, human checks, token budgets, output validation, and scope limits prevent expensive runaway behavior

![The Anatomy of an AI Agent — components and loop](../../images/20260528-1100-ai-agent-anatomy.png)

## Brain (LLM)

The LLM is the core decision-making component. It reads the situation, thinks, and decides what to do next. The fundamental shift from chatbots to agents is that models move from generating text to making autonomous choices about which actions to take.

## Planning

Agents decompose complex tasks into actionable steps using three methodologies:

- **Chain of Thought** — structured step-by-step reasoning through a problem
- **Tree of Thoughts** — explore multiple solution paths in parallel, evaluate and select the optimal one
- **Reflexion** — learn from mistakes by retrying with adjusted approaches based on prior failures

The purpose is converting vague objectives into concrete, executable action sequences.

## Tools

Tools give LLMs the ability to act on the world. The cycle: model requests a tool call (function name + arguments) -> runtime dispatches and executes -> result returns to the model as an observation.

Common tool categories:

- Web search
- Code execution (sandboxed)
- API calls
- File operations (read/write/edit)
- Browser automation

Tools are increasingly standardized via **MCP (Model Context Protocol)** — a common interface for connecting agents to external capabilities.

## Memory

Two categories operate in parallel:

- **Short-term** — the context window itself, holding the current conversation, tool results, and reasoning. Finite and reprocessed from scratch every turn.
- **Long-term** — vector stores, knowledge bases, and files that persist across sessions. Retrieved via top-k similarity search when relevant.

**Overflow strategy:** when the context window fills up, agents compact by summarizing old turns and carrying the summary forward. This trades detail for continuity.

## The Agent Loop

The core execution cycle:

1. **Perceive** — read the current state (user input, tool results, context)
2. **Plan** — reason about what to do next (chain of thought, reflect on past attempts)
3. **Act** — invoke a tool or produce a final answer
4. **Observe** — process the tool's result, update context

**Stop conditions:**

- Task complete (no more tool calls needed — final answer produced)
- Max iterations reached
- Token/cost budget exhausted

The loop terminates when any stop condition triggers. Without proper exit conditions, agents can loop indefinitely — a common production failure mode.

## Guardrails

Every action passes through a policy gate. The more autonomy you grant, the more these matter:

- **Sandboxing** — isolate agent execution to prevent unintended side effects
- **Human-in-the-loop** — require approval for high-risk actions
- **Token/cost budgets** — hard limits on spending per task
- **Output validation** — verify outputs meet format and safety constraints before returning
- **Scope limits** — restrict what tools and data the agent can access

Actions that fail validation are rejected before execution.

## 10-Step Maturity Ladder (From Runbook to Multi-Agent)

The complementary how-to framing: given a manual human runbook, here's how to convert it into an autonomous agent step-by-step. Each step pushes the human "to the right" — less involved in execution, more involved in oversight.

![10-step framework](../../images/20260605-1700-ai-agents-10-step-framework.png)

| Step | What | Outcome |
|---|---|---|
| 1 | Manual runbook | Baseline — human does everything |
| 2 | LLM analyzes (read-only) | LLM looks at logs/tickets, suggests; human acts |
| 3 | LLM uses MCP tools | LLM can fetch data via MCP; still suggests, doesn't act |
| 4 | Agent Skills | Reusable instructions for common tasks |
| 5 | Memory | Short-term context + Agents.md long-term facts |
| 6 | SOPs (RFC 2119 style — MUST/SHOULD/MAY) | Procedure encoded as agent-callable command |
| 7 | Packaged agent | Defined as a deployable agent definition |
| 8 | Periodic execution | Cron / tmux loop, runs unattended |
| 9 | Production integration | Webhooks, Lambda, Slack posting, ticket creation |
| 10 | Multi-agent orchestration | Sequential / routing / swarm patterns |

![Agent components and building blocks](../../images/20260605-1701-ai-agents-components.jpeg)

### Building-Block Roles Mapping

Each component maps to a human-role analog:

| Component | Role analog |
|---|---|
| LLM | Brain |
| Tools / MCP | Hands |
| Agent Skills + SOPs | Skills |
| Context window + Agents.md | Memory |
| Orchestrator | Manager |
| Cron / Scheduler | Alarm clock |
| Shared filesystem | Workspace |

### Multi-Agent Patterns (Step 10)

![Multi-agent orchestration](../../images/20260605-1708-ai-agents-multi-agent.png)

Three coordination styles:
- **Sequential** — agents run in order, output of one is input of next
- **Routing** — manager agent routes tasks to specialized subagents
- **Swarms** — agents collaborate via a shared filesystem / message bus

A manager agent coordinates subagents through a shared workspace (often a filesystem) — this is the deepest production pattern.

### Human-in-the-Loop Spectrum

![Human involvement spectrum](../../images/20260605-1709-ai-agents-human-spectrum.png)

> "Push the human to the right as much as possible."

The progression: start with the human approving every action; gradually move to the human only approving destructive/prod actions; eventually the human just reviews summaries.

But: **"no one will ask the AI for ownership."** Keep humans accountable even when agents execute — accountability scales differently than execution.

### Six Practical Implementation Lessons

1. Start with **read-only triage** before any write actions
2. Gather **many task examples** before encoding into an agent (you can't generalize from 2 cases)
3. **Iterate per step** — don't try to build all 10 steps at once
4. Monitor live via **tmux + logs** — agents are processes, treat them like processes
5. **Reliability ≠ prompt engineering** — it's testing, examples, observability, error handling
6. Push the human to the right; keep them accountable

## The 5-Layer Stack (Production View)

A complementary visualization that arranges the components into a **layered stack** with explicit product names per layer. Useful as a procurement / tooling-selection map.

![The AI Agent Stack — Model / Tools / Memory layers, ReAct loop runtime with orchestration components, observability + safety wrapper](../../images/20260614-1510-ai-agent-stack.jpeg)

### The Layers

| Layer | Role (body-part analog) | Examples |
|---|---|---|
| **Model Layer** | Brain — the underlying LLMs | OpenAI GPT-5, Anthropic Claude Sonnet 4 / Opus 4, Gemini 3.1 Pro |
| **Tool Layer** | Hands — interact with the real world | Search (SerpAPI, Tavily, Firecrawl); APIs (Stripe, GitHub, Notion); Code execution (E2B, Modal, Fly.io sandbox); Data access (Postgres, MySQL); calendar / filesystem / etc. |
| **Memory Layer** | Notebook — context lifecycle (Read context, Write experience) | **Short-term Working**: Redis session/cache, context buffer. **Long-term Semantic**: Qdrant, Pinecone, Milvus. **Long-term Transactional**: Postgres, MySQL |
| **Agent Runtime** | Executor — runs the ReAct loop + orchestration | (See below) |
| **Observability & Safety** | Wrapper — keeps it production-safe (cross-cutting) | Tracing/debugging: LangSmith; Eval/Feedback: Langfuse; Cost: Helicone; Quality/Drift: Arize Phoenix; Safety: Guardrails AI, Lakera, LlamaGuard |

### The ReAct Loop (4 Steps, Not Just 3)

The runtime runs an explicit **Thought → Action → Observation → Reflection** loop until the goal is reached:

1. **Thought (Reasoning)** — LLM decides what to do next
2. **Action (Tool Use)** — agent selects a tool and takes action
3. **Observation (Result)** — tool returns data or feedback
4. **Reflection (Plan Update)** — LLM updates context and decides the next step

The 4th step (Reflection) is the one that turns a stateless tool-call loop into an *agent* — the model explicitly updates its plan based on what just happened, not just on what the next tool call should be.

### Orchestration Components (Inside the Runtime)

The Runtime isn't just the loop — it's the loop plus 7 orchestration responsibilities that turn it from a research demo into a production system:

| Component | What it does |
|---|---|
| **Planning** | Decompose the goal into a sequence of steps |
| **Task Decomposition** | Break complex steps into sub-tasks |
| **Model Selection** | Route different sub-tasks to different models (cheap for triage, expensive for hard reasoning) |
| **Tool Selection** | Pick the right tool from a possibly-large catalog without flooding the context |
| **Execution Control** | Concurrency limits, timeouts, parallelism vs sequencing |
| **Error Handling** | Catch tool errors and decide whether to retry, replan, or escalate |
| **Retries & Recovery** | Backoff, idempotency, partial-success replay |

### Three-Way Memory Split (Refinement)

The earlier short-term/long-term split is sharpened in production into three distinct stores:

- **Short-term Working** — current task's scratchpad (Redis, in-memory context buffer)
- **Long-term Semantic** — accumulated knowledge / facts retrieved by similarity (vector DBs: Qdrant, Pinecone, Milvus)
- **Long-term Transactional** — durable state across steps (relational DBs: Postgres, MySQL) — bookings, user state, audit trail

The transactional layer is the one most people miss. Without it, your agent can plan and remember *facts*, but can't reliably track that "step 3 of 5 succeeded" across a crash.

## Related Patterns

- [Agentic design patterns](agentic-design-patterns.md) — the escalation ladder (when to use which pattern)
- [Multi-agent systems](../concepts/multi-agent-systems.md) — coordination styles in depth
- [LLM tool use and MCP](../concepts/llm-tool-use-and-mcp.md) — step 3 (MCP tools) in depth
- [Context engineering](../concepts/context-engineering.md) — step 5 (memory) in depth
- [Agent memory + state consistency](agent-memory-state-consistency.md) — state vs memory split, three-tier memory in depth
- [OpenAI data agent](openai-data-agent.md) — production case with all 10 steps active
- [Stripe Minions](stripe-minions.md) — production case at step 10
- [Harness engineering](harness-engineering.md) — production case at step 10 with mechanically-enforced invariants

---

**Source:** https://blog.bytebytego.com/p/ep215-the-anatomy-of-an-ai-agent
**Source:** https://newsletter.systemdesign.one/p/how-do-ai-agents-work
**Source:** https://blog.bytebytego.com/i/201645658/the-typical-ai-agent-stack-explained
**Date:** 2026-05-28 (initial), 2026-06-05 (10-step ladder), 2026-06-14 (5-layer stack with product map + ReAct's 4 steps + orchestration components + transactional memory)
**Tags:** agents, llm, agent-loop, planning, tools, memory, guardrails, mcp, react, runbook, multi-agent, sop, agent-maturity-ladder, agent-stack, observability, langsmith, langfuse, helicone, llamaguard, lakera, transactional-memory, orchestration-components, react-loop
