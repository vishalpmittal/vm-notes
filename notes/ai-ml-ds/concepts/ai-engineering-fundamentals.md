# AI Engineering Fundamentals

## Key Takeaways

- Three eras: Software 1.0 (hand-written code) → 2.0 (learned from data) → 3.0 (natural language as programming interface, agents)
- An LLM is best understood as a "non-deterministic database" — the model is the DB, the prompt is the query
- Context is finite working memory (like RAM) — doesn't persist between sessions, degrades before hitting hard limits, and must be actively managed
- Evals replace traditional tests for non-deterministic systems — use LLM-as-a-judge with curated input/output datasets
- Agent loops (ReAct pattern: Thought → Action → Observation → Repeat) are what make Software 3.0 agentic

## Software 1.0, 2.0, and 3.0

- **1.0:** Hand-written logic via code — inception of programming through to ML era
- **2.0:** Logic learned from data, not instructions — e.g., Tesla Autopilot shifting from C++ rules to neural networks
- **3.0:** Natural language as programming interface — vibe coding, agents that think and act autonomously

## Large Language Models

- Trained on massive text datasets to predict continuations; learn semantic relationships between words
- Non-deterministic by design — different answers for same input
- Mental model: **LLM = non-deterministic database**, prompt = query that extracts info in desired format
- Also: **LLM = CPU** of your AI application — it makes decisions on its own given information
- Choosing an LLM is like picking a CPU: tradeoffs across latency, cost, and functionality

## Context

- Working memory for a task — all input converted to tokens (~3-4 chars each), processed via attention mechanism
- **Entire context reprocessed from scratch each turn** — no memory of previous turns unless full history is fed back in
- Degrades before hitting hard limit: models bias toward start and end of context window
- Frontier models: 1M+ tokens; open models: ~256K tokens
- Management strategies: subagents (separate context windows), summarize-and-clear, external storage

## Embeddings and Vector Databases

- Embedding = meaning of data as a vector (list of numbers); similar meaning = close vectors
- Vector DBs store and query embeddings using similarity-optimized algorithms (like a semantic cache)
- Retrieval flow: Index (docs → embeddings) → Retrieve (embed query, find similar) → Augment (add to context) → Generate (LLM answers)

## Time to First Token

- **TTFT:** how long until the model starts responding — determines perceived responsiveness
- **Tokens/sec (throughput):** generation speed after starting
- Streaming output reduces perceived latency (like video streaming — play while loading)

## Evals

- Traditional assert-based tests don't work for non-deterministic outputs
- Use **LLM-as-a-judge**: curate dataset of input/expected-output pairs, have a judge LLM score the tested model's outputs on criteria (relevance, hallucinations, accuracy)
- Critical because even small model tweaks can drastically change system output

## Agent Loops (ReAct Pattern)

- **Thought** → **Action** (tool call) → **Observation** (tool result) → **Repeat** until task complete
- Merges chain-of-thought reasoning with tool execution in a single loop
- Enables complex workflows impossible with written code alone

## Tool Calling

- LLMs can't execute code, access internet, or perform work natively — tool calling enables function execution
- Output of LLM triggers function calls that perform actual work

---

**Date:** 2026-05-28
**Tags:** llm, context-window, embeddings, vector-db, evals, agent-loop, react, tool-calling, software-engineering
