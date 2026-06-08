# AI Glossary — Concepts in Common Use

A quick-reference glossary for terms that get used in AI conversations without consistent meaning. One-line definitions plus links to deeper notes where they exist.

## Key Takeaways

- **AI is now systems engineering**, not just prompt tweaking — tokens, context, retrieval, tools, evals, and observability all shape behavior
- **Context window is working memory, not knowledge storage** — what you put in it (and in what order) is an engineering decision
- **Generation pattern matters more than model choice** — RAG, tool calling, and agents shift the failure surface; none eliminate hallucination on their own
- **Production AI requires evals, grounding, guardrails, observability** — "vibes-based" judgment doesn't scale past a demo
- **Efficiency techniques (LoRA, quantization, distillation)** decouple capability from deployment cost — smaller specialized models often win in production

> "AI is no longer just about asking a model better questions. It is about building better systems around the model."

## How the 25 Concepts Group

```
Model internals          Information layer        Systems layer
─────────────────        ──────────────────       ──────────────
1. Tokens                6. Embeddings            13. Tool Calling
2. Next-Token Prediction 7. Vector Databases      14. Function Calling
3. Context Window        8. Semantic Search       15. Agents
4. Attention             9. Retrieval             16. Fine-Tuning
5. Transformers          10. RAG                  17. LoRA
                         11. Prompting            18. Quantization
                         12. Context Engineering  19. Distillation
                                                  20. Inference
                                                  21. Evals
                                                  22. Hallucination
                                                  23. Grounding
                                                  24. Guardrails
                                                  25. Observability
```

## Model Internals

### 1. Tokens
Sub-word units (word fragments, punctuation, whitespace, symbols) the model actually sees. Affect context length, cost, latency, and where prompts get truncated. "Tokens" ≠ words — `tokenization` of "tokenization" is often 3 tokens.

### 2. Next-Token Prediction
The core LLM operation: given the prior tokens, output a probability distribution over the next token. The "decoding strategy" (greedy, top-p, temperature) picks which one. Everything else (chat, agents, RAG) is layers on top.
→ See [Transformer architecture § End-to-End LLM Flow](transformer-architecture.md#end-to-end-llm-flow)

### 3. Context Window
The total information visible per inference call: system prompt + user message + conversation history + retrieved docs + tool results. **Working memory, not permanent knowledge.** Once it overflows, something gets dropped.
→ See [Agent memory and state consistency](../agents/agent-memory-state-consistency.md)

### 4. Attention
The mechanism that lets each token "weigh" information from other tokens. In decoder-only LLMs, **causal self-attention** restricts each token to looking at prior tokens only.
→ See [Transformer architecture § Causal Multi-Head Self-Attention](transformer-architecture.md#1-causal-multi-head-self-attention)

### 5. Transformers
The architecture centered on attention rather than recurrence or convolution. Decoder blocks combine attention, feed-forward network, residuals, and normalization, stacked N times.
→ See [Transformer architecture](transformer-architecture.md) (the full note)

## Information Layer

### 6. Embeddings
Vector representations of text where **semantic similarity = spatial closeness**. Embedding quality is task-dependent — "best embedding model" depends on the retrieval task, not an absolute benchmark.
→ See [Vector databases](vector-databases.md)

### 7. Vector Databases
Storage and nearest-neighbor retrieval over embeddings. The real engineering work is chunking, metadata, freshness, permissions, and pre/post-filtering — not picking pgvector vs. Pinecone.
→ See [Vector databases](vector-databases.md)

### 8. Semantic Search
Matching by *meaning* rather than exact keywords. "How do I cancel my subscription?" should match a doc titled "Membership termination process." Hybrid (semantic + keyword) usually beats either alone.
→ See [Vector databases](vector-databases.md), [Instacart search infrastructure](../../system-design/case-studies/instacart-search-infrastructure.md)

### 9. Retrieval
Bringing external information into the model at query time. Covers chunking strategy, indexing choice, filtering rules, ranking, and context assembly. Retrieval quality is usually the gating factor on RAG accuracy.

### 10. RAG (Retrieval-Augmented Generation)
The pattern: retrieve relevant docs, stuff them into the context window, ask the model to answer using them. **Separates "where the knowledge lives" from "how the answer is composed,"** but doesn't guarantee correctness — retrieval misses cascade into hallucinated answers.
→ See [RAGs vs Agents](rag.md#when-to-reach-for-rag-vs-agents-vs-long-context)

### 11. Prompting
The instruction layer specifying task, role, format, constraints. Guides behavior but **doesn't update weights or add knowledge** — knowledge has to come from training, retrieval, or tools.

### 12. Context Engineering
The deliberate decisions about what the model sees: prompts, retrieval, history, tool outputs, memory. **Content, order, quality, and freshness all matter.** Often the highest-leverage work in a production AI system.
→ See [OpenAI's internal data agent](../agents/openai-data-agent.md) (six-layer context assembly case study)

## Systems Layer

### 13. Tool Calling
The model proposes a call to an external system (API, DB, function); the application validates and executes; the result goes back into context. Permissions and safety stay in software, not in the model.
→ See [LLM tool use and MCP](llm-tool-use-and-mcp.md)

### 14. Function Calling
A structured form of tool calling where the model returns schema-conformant arguments (JSON) instead of free-form text. Easier to parse, validate, route, and test. "Function calling" is the OpenAI-API marketing term; mechanically it's the same pattern as tool calling with stricter output shape.
→ See [LLM tool use and MCP](llm-tool-use-and-mcp.md)

### 15. Agents
Looping systems that plan, call tools, observe results, update state, decide next steps. **Bounded agents** (with a defined task and exit condition) usually beat fully autonomous ones.
→ See [Agentic design patterns](../agents/agentic-design-patterns.md), [AI agent anatomy](../agents/ai-agent-anatomy.md)

### 16. Fine-Tuning
Continued training of a pretrained model on a narrower dataset. **Updates the weights** to encode patterns, terminology, or formatting. Different from prompting (no weight changes) and from context engineering (changes what the model sees, not what it knows).

### 17. LoRA (Low-Rank Adaptation)
Parameter-efficient fine-tuning: **freeze the base model**, train small low-rank matrices in selected layers. Cuts memory and compute dramatically — fine-tune a 70B model on a single GPU instead of a cluster. The de facto default for fine-tuning since 2023.

### 18. Quantization
Reducing numeric precision (e.g., FP32 → INT8, or further to INT4) for lower memory, lower bandwidth, and often faster inference. Small quality cost in exchange for being able to fit a larger model in the same hardware budget. Modern formats (GPTQ, AWQ, GGUF) push this further with minimal accuracy loss.

### 19. Distillation
Train a smaller "student" model to imitate a larger "teacher" model. Transfers useful behavior at lower deployment cost. The student isn't as capable on edge cases but often matches the teacher closely enough on the target distribution at a fraction of the inference cost.

### 20. Inference
Running trained models in production. Latency, cost, throughput, hardware (CPU/GPU/TPU/accelerator), context length, KV-cache management, and request batching all matter. **At scale, inference cost dominates training cost.**
→ See [ML systems at scale](../inference/ml-systems-at-scale.md)

### 21. Evals
Tests measuring accuracy, format adherence, style, retrieval quality, safety, and latency. Replace "this looked good when I tried it" with reproducible measurement. **Evals are the unit tests of LLM systems.**
→ See [LLM evals](llm-evals.md)

### 22. Hallucination
Plausible-sounding but unsupported or incorrect output — fake citations, made-up facts, wrong math. Models generate likely-looking text; they don't verify truth. Mitigation requires grounding + evals + guardrails, not just better prompts.

> "Fluency is not evidence. Confidence is not correctness."

### 23. Grounding
Anchoring answers to evidence (docs, databases, tool results, calculations, citations). Improves traceability and reduces hallucination — but only if the evidence is itself correct and complete. Grounding on bad sources just makes wrong answers harder to detect.

### 24. Guardrails
Layered controls on inputs, outputs, tool calls, data access, permissions, and schemas. **Layers outperform single-point filters** — input filtering + output filtering + tool-level authz + audit logging is much more robust than one big classifier.
→ See [Prompt injection defenses](prompt-injection-defenses.md), [Agent identity and auth](agent-identity-and-auth.md)

### 25. Observability
Full execution visibility: prompts, retrieved documents, tool calls (args, return values), latency per step, failure modes. Without it, debugging a failed agent run is guesswork. The minimum bar for production AI.

## The Reliability Stack (Concepts 21–25 Together)

Single techniques are insufficient. The reliability stack:

```
   Observability    ── visibility into what happened
   Guardrails       ── controls on what can happen
   Evals            ── measurement of what is happening
   Grounding        ── evidence behind what's said
   (Hallucination)  ── the failure mode all of the above defend against
```

## The Efficiency Stack (Concepts 17–19 Together)

Three independent levers for cost/latency:

| Lever | What it changes |
|---|---|
| **LoRA** | How the model is *adapted* — keeps base model frozen |
| **Quantization** | The model's *precision* — fewer bits per parameter |
| **Distillation** | The model's *size* — fewer parameters |

Stackable. Quantizing a distilled model with LoRA-tuned adapters is normal.

## Related Notes (Deeper Dives)

- [Transformer architecture](transformer-architecture.md) — concepts 2, 4, 5 in depth
- [Vector databases](vector-databases.md) — concepts 6, 7, 8
- [RAGs vs Agents](rag.md#when-to-reach-for-rag-vs-agents-vs-long-context) — concepts 10, 15
- [LLM tool use and MCP](llm-tool-use-and-mcp.md) — concepts 13, 14
- [Agentic design patterns](../agents/agentic-design-patterns.md) — concept 15
- [AI agent anatomy](../agents/ai-agent-anatomy.md) — concept 15
- [Agent memory and state consistency](../agents/agent-memory-state-consistency.md) — concept 3
- [LLM evals](llm-evals.md) — concepts 21, 22
- [Prompt injection defenses](prompt-injection-defenses.md) — concept 24
- [Agent identity and auth](agent-identity-and-auth.md) — concept 24
- [AI engineering fundamentals](ai-engineering-fundamentals.md) — broader framing
- [ML systems at scale](../inference/ml-systems-at-scale.md) — concept 20

---

**Source:** https://thecuriousmak.substack.com/p/25-ai-concepts-people-use-but-dont
**Date:** 2026-06-04
**Tags:** ai, llm, glossary, tokens, attention, transformers, embeddings, vector-databases, rag, prompting, context-engineering, tool-calling, agents, fine-tuning, lora, quantization, distillation, inference, evals, hallucination, grounding, guardrails, observability
