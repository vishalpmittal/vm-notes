# Multi-Agent Systems and Workflow Patterns

## Key Takeaways

- Start with one agent — only go multi-agent when you hit context overflow, need parallelism, or require specialization (different tools/models/permissions)
- Three coordinated architectures (orchestrator-worker, pipeline, hierarchical) and four workflow patterns (chaining, routing, parallelization, orchestrator-workers) cover most production needs
- Most production systems shouldn't need to go beyond parallelization; orchestrator-workers introduces unpredictable failure modes
- Key tradeoff across all patterns: more coordination power = more cost and harder debugging

## When to Go Multi-Agent

![Multi-agent decision flow](../../images/20260528-1201-multi-agent-decision-flow.jpg)

Three hard limits that better prompts won't fix:

- **Context overflow** — context window full, earliest info drops out, agent loses track of its plan
- **Parallelism** — independent tasks shouldn't wait in line (Anthropic's research system reduced query time by 90% with parallel agents)
- **Specialization** — different parts need different models, tools, or access levels (code agent needs sandbox, research agent needs web, customer agent needs user data)

If none apply, stay with one agent.

## Multi-Agent Architectures

### Orchestrator-Worker

One central agent breaks tasks, assigns to workers, combines results. Workers don't talk to each other.

![Orchestrator-Worker](../../images/20260528-1202-orchestrator-worker-architecture.jpg)

- **Example:** Claude Research — Opus 4 orchestrator spawns 2-10+ Sonnet 4 workers in parallel; beat single-agent Opus 4 by 90.2% on internal evals
- **Tradeoff:** central agent is the bottleneck (~7 tasks/sec if each call takes 3s); vague task splitting turns parallelism into duplicated work

### Pipeline

Agents run in fixed order, each agent's output becomes the next's input. Sequence set at design time.

![Pipeline](../../images/20260528-1203-pipeline-architecture.jpg)

- **Example:** Stripe fraud review — DAG of agent stages for business verification; cut handling time by 26%, 96% helpfulness rating
- **Tradeoff:** latency adds up linearly (5 stages x 2s = 10s); but every failure traces to exactly one step

### Hierarchical

Tree of managers and workers. Orders flow down, summaries flow up. No single agent needs full context.

![Hierarchical](../../images/20260528-1204-hierarchical-architecture.jpg)

- **Example:** IBM watsonx Orchestrate — top supervisor routes across 80+ domain agents (HR, sales, procurement); child agents handle vendor quotes, compliance, purchase requests
- **Tradeoff:** details get lost at each level as results get summarized upward

## Workflow Patterns (Code Controls the Flow)

### Prompt Chaining

Sequential LLM calls with validation gates between steps. Like a CI/CD pipeline — each step must pass before the next runs.

![Prompt Chaining](../../images/20260528-1205-prompt-chaining-pattern.jpg)

- **Example:** Legal contract review — extract clauses, classify by risk, summarize high-risk items
- **Tradeoff:** latency grows linearly; errors carry forward past what gates can catch

### Routing

Classify input, send to the right handler (different prompt, model, or sub-workflow).

![Routing](../../images/20260528-1206-routing-pattern.jpg)

- **Example:** Sierra AI routes across 15+ models; Intercom Fin routes by intent and sentiment to AI or human
- **Cost benefit:** simple queries hit cheap models, complex ones hit expensive models
- **Tradeoff:** misclassification is a single point of failure; router accuracy caps system accuracy

### Parallelization

Two variants: **sectioning** (split into independent parts, merge) and **voting** (same task multiple times, aggregate).

![Parallelization](../../images/20260528-1207-parallelization-sectioning-voting.jpg)

- **Sectioning example:** GitHub Advanced Security — CodeQL + Snyk + Semgrep scan same PR in parallel
- **Voting example:** security review — 3 prompts analyze same code, flag only if 2+ agree
- **Tradeoff:** cost multiplies per branch; partial failure handling must be designed upfront

### Orchestrator-Workers (Dynamic)

Central LLM decides subtasks at runtime (unlike pipeline where steps are predetermined).

![Orchestrator-Workers](../../images/20260528-1208-orchestrator-workers-pattern.jpg)

- **Example:** Cursor agent mode — dynamically spawns agents for tests, docs, refactoring
- **Not multi-agent:** single central LLM stays in control; in true multi-agent, agents can call each other directly
- **Tradeoff:** orchestrator can lose track of the original goal or become a bottleneck

---

**Date:** 2026-05-28
**Tags:** multi-agent, orchestrator, pipeline, hierarchical, workflow-patterns, prompt-chaining, routing, parallelization
