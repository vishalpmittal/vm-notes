# GenAI System Design Fundamentals

A foundational survey of generative AI system architecture: model families (autoregressive vs diffusion), architecture variants, deployment tiers, and the data pipeline that determines what any model can actually do.

## Key Takeaways

- GenAI systems split into two **core generative families**: autoregressive (GPT, Claude, Llama — generate token-by-token, latency scales linearly) and diffusion (Midjourney, Stable Diffusion, Sora — iterate from noise, cost ~10-100× per image vs a chat response)
- **Model selection is downstream of data quality** — foundation models train on trillions of tokens passed through dedup, cleaning, filtering, transformation, plus synthetic augmentation and bias auditing. Get the pipeline wrong and no architecture saves you
- **Architecture variants map to use cases**: decoder-only for generation (GPT/Claude/Llama), encoder-only for classification (BERT), encoder-decoder for translation/summarization (T5/BART), MoE for capacity without proportional compute (Mixtral 8x7B = 46.7B total / ~12.9B active per token)
- **Three deployment tiers**: proprietary (highest capability, API-only), open-weight (Llama/Mistral, you run infra), open-source (weights + code + data + methodology). Production systems route hybrid — simple to open, complex to proprietary
- **Latency budgets differ by modality**: autocomplete <300ms, chat = brief pause, image gen 10-60s. Benchmarks (MMLU, HumanEval, Chatbot Arena, GPQA) are useful but contamination means domain-specific evals are required

> The article is **part 1 of a paid series**. The summary captures the publicly-accessible content; deeper sections on ChatGPT/Perplexity/Copilot reference architectures are paywalled.

## Two Core Generative Families

### Autoregressive Models

Generate output token-by-token, conditioning each new token on prior tokens:

```
"The cat" → ["The cat sat"] → ["The cat sat on"] → ["The cat sat on the"] → ...
```

- **Latency** scales linearly with output length — a 500-token response = ~500 generation steps
- **Cost** scales with output length (and input length)
- **Examples**: GPT-5, Claude, Gemini, Llama, DeepSeek

See [transformer-architecture.md](transformer-architecture.md) for the architecture under the hood.

### Diffusion Models

Start from pure random noise and iteratively denoise toward a coherent output:

```
random noise → step 1 (less noisy) → step 2 → ... → step 30 (clean image)
```

- **Latency** is set per step; typical 20-50 steps for images, takes seconds to minutes
- **Cost** ~10-100× a typical chat response per image
- Works as **batch/queue jobs** — image generation is rarely sub-second
- **Examples**: Midjourney, Stable Diffusion, DALL-E, Sora (video)

See [image-generation-diffusion.md](image-generation-diffusion.md) for deeper.

## Output Modalities

Five categories of generative output, each with its own model family/architecture:

| Modality | Examples |
|---|---|
| **Text generation** | GPT-5, Claude, Llama, Gemini |
| **Text-to-image** | Midjourney, DALL-E 3, Stable Diffusion |
| **Text-to-video** | Sora 2, Runway Gen-3, Kling, Veo 3.1 |
| **Text-to-audio** | ElevenLabs, Suno, Bark |
| **Vision-language multimodal** | GPT-5, Claude, Gemini (one model, text + image input/output) |

### Cross-Modal Embeddings

Multimodal models use cross-modal embeddings (e.g., **OpenAI CLIP**) to map text and images into a shared vector space. This is what lets a text prompt guide image generation, or what lets a vision-language model answer questions about an image.

## Architecture Variants

Different transformer variants for different tasks:

| Variant | Strength | Examples |
|---|---|---|
| **Decoder-only** | Text generation | GPT, Claude, Llama, Gemini |
| **Encoder-only** | Classification, embedding | BERT, RoBERTa |
| **Encoder-decoder** | Translation, summarization | T5, BART, Pegasus |
| **Mixture of Experts (MoE)** | Capacity without compute proportional to parameter count | Mixtral 8x7B (46.7B total, ~12.9B active per token) |

MoE is the most interesting recent trend — selective activation means a 100B-parameter model can run with the compute cost of a 20B model. See also [transformer-architecture.md § Modern Attention Variants](transformer-architecture.md#modern-attention-variants-the-efficiency-ladder).

## Deployment Tiers

| Tier | Examples | What you get | What you give up |
|---|---|---|---|
| **Proprietary** | GPT-5, Claude, Gemini | Highest capability, managed infra | API-only, no control, vendor lock-in |
| **Open-weight** | Llama, Mistral, Gemma, Qwen | Full parameter access, self-hosted | Training data undisclosed, you run infra |
| **Open-source** | gpt-oss, OLMo | Weights + code + data + methodology | Often smaller / less capable; you run everything |

The gap between proprietary and open-weight **narrowed dramatically through early 2025** (the "DeepSeek moment" — see [ai-trends-2026.md](ai-trends-2026.md)). Most production systems now **hybridize via routing**:
- Simple queries → open-weight (cheap, fast, private)
- Complex reasoning → proprietary (capability matters more than cost)

## Model Selection Criteria

### Size Tradeoffs

| | Smaller models | Larger models |
|---|---|---|
| Speed | Fast | Slow |
| Cost | Cheap | Expensive |
| Capability | Limited | Strong |
| Deployment | Edge / consumer hardware | Cloud GPUs / clusters |

### Reasoning Modes

Extended-thinking / reasoning modes (o1, R1, Gemini 3 thinking) are essential for math, logic, multi-step problems. Configurable in modern models — pay more compute for harder problems.

### Benchmarks

| Benchmark | Tests |
|---|---|
| **MMLU** | 57 academic subjects |
| **HumanEval / MBPP** | Code generation |
| **Chatbot Arena** | Human preference |
| **GPQA / ARC-AGI** | Reasoning |

**Benchmark contamination is widespread** — models have likely seen these benchmarks in training. Use them for shortlisting, then run **domain-specific evals** on real-world tasks for the actual selection decision. See [llm-evals.md](llm-evals.md).

## The Data Layer

Foundation models train on **trillions of tokens** — books, web, code, papers, forums. The pipeline matters more than the architecture for actual capability.

### Training Data Pipeline

```
raw corpus
   ↓
[1] Deduplication       — prevents pattern over-learning
   ↓
[2] Cleaning            — toxic content, PII removal
   ↓
[3] Filtering           — low-quality / spam removal
   ↓
[4] Transformation      — format normalization
   ↓
[5] Synthetic augmentation — fill underrepresented domains
   ↓
[6] Bias auditing       — measure before training
   ↓
training set
```

Synthetic data is increasingly critical for under-served domains (math derivations, code with explanations, multi-turn dialogues, code reviews).

### Data Storage & Retrieval

- **Training**: HDFS or cloud object storage (S3) streamed in parallel batches to GPU clusters
- **Inference**: model parameters loaded into GPU memory (hundreds of GBs for large models)
- **Runtime retrieval**: for live systems (e.g., Perplexity pulling search results), retrieval happens per-query and feeds into the prompt (see [rag.md](rag.md))

## Latency Budgets by Use Case

| Use case | Acceptable latency |
|---|---|
| Code autocomplete | <300ms |
| Chat response | Brief pause OK |
| Long-form reasoning | 1-30s (with thinking modes) |
| Image generation | 10-60s |
| Video generation | Minutes to hours |

Users disengage after ~3s without feedback. Streaming generation, partial results, and "thinking" indicators are how production systems mask longer latencies.

## Glossary of Essential Terms

| Term | Definition |
|---|---|
| **Token** | ~4 English chars; the unit of pricing and context limits |
| **Embedding** | Numerical vector capturing meaning |
| **Parameter** | A weight in the model (GPT-3: 175B, Llama 4 Scout: 17B active) |
| **Context window** | Max tokens visible per call (GPT-5: 400K, Claude/Gemini: 1M) |
| **Inference** | Running the trained model (measured by TTFT and TPS) |
| **TTFT** | Time to first token |
| **TPS** | Tokens per second |
| **LLM** | Large Language Model |
| **Foundation model** | Pre-trained model trained on broad data, used as a base for many tasks |
| **RAG** | Retrieval-Augmented Generation; see [rag.md](rag.md) |
| **Fine-tuning** | Continued training on narrower data; affects style/format, not knowledge |
| **Prompt** | The input to the model |
| **Hallucination** | Plausible-sounding but unsupported output |
| **Transformer** | The attention-based architecture (2017 paper) |
| **GPU/TPU** | NVIDIA H100/A100/B200, Google TPU — see [cpu-gpu-tpu.md](../inference/cpu-gpu-tpu.md) |

Full glossary: [ai-glossary.md](ai-glossary.md).

## What This Article Doesn't Cover (Paywalled Sections)

The paid continuation reportedly covers:
- ChatGPT, Perplexity, Copilot reference architectures
- Production prompt engineering patterns
- Multi-modal model serving specifics
- Agent integration patterns

For these, see:
- [Claude Code architecture](../claude/claude-code-architecture.md) and [OpenAI Codex](../agents/openai-codex.md) for production agent architectures
- [LinkedIn dual-encoder ranking](llms-in-production-ranking.md) for a production embedding/RAG case
- [OpenAI data agent](../agents/openai-data-agent.md) for production context assembly

## Related

- [Transformer architecture](transformer-architecture.md) — decoder-only / encoder-only / encoder-decoder / MoE in depth
- [AI glossary](ai-glossary.md) — quick reference for all the terms
- [AI engineering fundamentals](ai-engineering-fundamentals.md) — broader engineering framing
- [Image generation: diffusion](image-generation-diffusion.md) — diffusion vs autoregressive in depth
- [Context engineering](context-engineering.md) — how the data layer extends into runtime
- [RAG](rag.md) — runtime retrieval architecture
- [LLM evals](llm-evals.md) — domain-specific evaluation
- [ML systems at scale](../inference/ml-systems-at-scale.md) — production inference architecture
- [CPU vs GPU vs TPU](../inference/cpu-gpu-tpu.md) — the hardware substrate

---

**Source:** https://newsletter.systemdesign.one/p/generative-ai-system-design
**Date:** 2026-06-05
**Tags:** genai, system-design, llm, foundation-models, autoregressive, diffusion, decoder-only, moe, training-data, open-weight, proprietary, benchmarks, paywalled
