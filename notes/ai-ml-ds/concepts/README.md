# ai-ml-ds/concepts — RAG, LLM patterns, ML/GenAI system design, evals, fundamentals

The general-knowledge layer: RAG, LLM/ML patterns, GenAI system design, evals, fundamentals and glossaries, plus writing with LLMs and AI security. Company case studies of agents live in agents/; multi-agent/harness engineering lives in agent-teams-harness-eng/; model serving and hardware live in inference/.

## Notes
- [agent-identity-and-auth.md](agent-identity-and-auth.md) — identity and delegation for agents; Uber's act_chain JWT model
- [ai-assisted-code-review.md](ai-assisted-code-review.md) — using LLMs deliberately as code-review agents to raise quality
- [ai-coding-productivity-studies.md](ai-coding-productivity-studies.md) — research synthesis: AI shifts the bottleneck to review and delivery
- [ai-engineering-discipline.md](ai-engineering-discipline.md) — code as disposable cache; relocating rigor to tests and observability
- [ai-engineering-fundamentals.md](ai-engineering-fundamentals.md) — Software 1.0/2.0/3.0, LLM as non-deterministic DB, context, evals
- [ai-glossary.md](ai-glossary.md) — quick-reference glossary of LLM-era AI terms
- [ai-security-platform.md](ai-security-platform.md) — enterprise AI security taxonomy: risks, outcomes, platform capabilities
- [ai-trends-2026.md](ai-trends-2026.md) — five mature AI trends reinforcing each other in 2026
- [amazon-cosmo-llm-recommendations.md](amazon-cosmo-llm-recommendations.md) — LLM-generated knowledge graph for product recommendations
- [context-engineering.md](context-engineering.md) — architecting the runtime information environment; Write/Select/Compress/Isolate
- [customer-churn-prediction.md](customer-churn-prediction.md) — churn prediction from text reviews + metadata via transformer embeddings
- [fine-tuning.md](fine-tuning.md) — fine-tune for behavior, retrieve for knowledge; SFT, LoRA, ship gates
- [genai-project-structure.md](genai-project-structure.md) — reference repo/directory layout for a production GenAI app; config, data, src, scripts
- [genai-system-design.md](genai-system-design.md) — GenAI architecture survey: model families, deployment tiers, data pipeline
- [image-generation-diffusion.md](image-generation-diffusion.md) — autoregressive vs diffusion image generation paradigms
- [llm-cost-and-routing.md](llm-cost-and-routing.md) — why agent token spend explodes and how model routing cuts cost
- [llm-evals.md](llm-evals.md) — evals for non-deterministic systems; LLM-as-judge, golden sets, RAG triad
- [llm-multi-pass-pipelines.md](llm-multi-pass-pipelines.md) — split creative from structural work across multiple LLM passes
- [llm-tool-use-and-mcp.md](llm-tool-use-and-mcp.md) — function calling, MCP, and A2A protocols
- [llm-vs-slm-design.md](llm-vs-slm-design.md) — LLM vs SLM tradeoffs; deployment, economics, training constraints
- [llms-in-production-ranking.md](llms-in-production-ranking.md) — LinkedIn's unified LLM dual-encoder retrieval/ranking system
- [mcp-vs-agent-skills.md](mcp-vs-agent-skills.md) — MCP protocol vs Skills folders across five dimensions
- [ml-fundamentals.md](ml-fundamentals.md) — classical ML/DS glossary; AI⊃ML⊃DL, supervised vs unsupervised, scikit-learn
- [ml-system-design-interview.md](ml-system-design-interview.md) — end-to-end ML pipeline design for interviews
- [multi-agent-systems.md](multi-agent-systems.md) — when to go multi-agent; coordination architectures and workflow patterns
- [multimodal-llms.md](multimodal-llms.md) — unifying text/image/audio/video into a shared embedding space
- [notebooklm-research-workflow.md](notebooklm-research-workflow.md) — piping NotebookLM's knowledge graph into Obsidian via Claude Code
- [open-weight-models.md](open-weight-models.md) — open-weight LLM ecosystem, MoE, and purpose-fit model selection
- [prompt-injection-defenses.md](prompt-injection-defenses.md) — stacked defenses against prompt injection; planner/executor split
- [rag.md](rag.md) — RAG from classic one-shot pipeline to agentic loops and Graph RAG
- [roblox-ai-translation.md](roblox-ai-translation.md) — real-time multilingual chat translation at 5,000 chats/sec
- [transformer-architecture.md](transformer-architecture.md) — decoder-only Transformer internals and modern component variants
- [vector-databases.md](vector-databases.md) — similarity search, HNSW indexing, quantization, and when to use them
- [writing-with-llms.md](writing-with-llms.md) — using LLMs for phrasing only; topic vs thesis; when writing should exist
