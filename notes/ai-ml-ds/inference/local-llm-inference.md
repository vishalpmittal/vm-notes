# Running LLMs Locally

Local LLM inference moved from "pipe dream" to "Tuesday afternoon project" in ~24 months. Open models (Qwen, GLM family, Llama, Gemma) plus a maturing inference-engine stack mean **a hosted API is now a choice, not a requirement** — driven by data privacy, predictable cost, and "AI sovereignty" concerns.

## Key Takeaways

- **`llama.cpp` + GGUF is the foundation.** A portable C++ runtime + a single-file packaging format (weights + tokenizer + metadata) makes 4-bit-quantized models fit on consumer hardware. Almost every other local tool wraps it
- **Two UX paths for personal use:** **Ollama** (CLI wrapper with OpenAI-compatible API — for developers) vs **LM Studio** (GUI with HuggingFace browser + RAM warnings — for casual use)
- **For production-grade serving, vLLM and SGLang dominate.** Both solve the **KV cache memory bottleneck** — vLLM via **PagedAttention** (virtual-memory-style paging), SGLang via **RadixAttention** (tree-cached prompt prefixes for shared-document workloads)
- **MLX-LM is Apple's unfair advantage.** Apple Silicon's **unified memory** (CPU + GPU share one pool, up to 192 GB on Mac Studio) means a single Mac can run "frontier-class" models that would otherwise need 4–5 A100 GPUs
- **Quantization (4-bit and lower) is the breakthrough** that made all this viable — it compresses model weights enough to fit on consumer hardware without significant intelligence loss

## The Tool Matrix

![How to run LLMs locally — Prototyping/Everyday Use vs Performance/Production Serving, with use-case → tool → core-benefit table](../../images/20260614-1730-running-llms-locally-tool-matrix.png)

| If you want to... | Use this tool | Why |
|---|---|---|
| Browse and chat via a GUI | **LM Studio** | No-code interface, RAM/GPU-offload warnings |
| Integrate AI into code quickly | **Ollama** | Fast setup, OpenAI-compatible API |
| Serve high-traffic production apps | **vLLM / SGLang** | Production-ready concurrency + throughput |
| Max out performance on a Mac | **MLX-LM** | Exploits Apple Silicon unified memory |
| Target edge or custom hardware | **llama.cpp** | Lightest possible C++ runtime |

## The Foundation: llama.cpp + GGUF

`llama.cpp` started as a side project to run Llama on a MacBook. It's now the C++ inference engine under nearly the entire local ecosystem. Its advantage is **portability** — plain C++ runs on CPU, GPU, and Apple Silicon with minimal dependencies.

The bigger breakthrough is **GGUF** — a single file that bundles weights + tokenizer + metadata, enabling **quantization** down to 4-bit (or lower). That's what makes a 70B-parameter model fit on a laptop. Ollama, LM Studio, and most others read GGUF files and call llama.cpp underneath.

> "A GGUF file packs the weights, tokenizer, and metadata into one file... which is what makes large models fit on consumer hardware."

## Personal Use: Ollama vs LM Studio

| Feature | Ollama (terminal) | LM Studio (GUI) |
|---|---|---|
| **Primary audience** | Developers and engineers | Casual users and hobbyists |
| **Interface** | CLI + REST API | Desktop application |
| **Key advantage** | OpenAI-compatible API (one-line `base_url` swap from cloud to local) | Visual GPU-offload settings + RAM warnings before downloading too-big models |
| **Best for** | Fast prototyping, app integration, scripting | Browsing models, comparing quantizations, chatting without code |
| **Workflow** | `ollama run gemma2` → pulls weights, starts a local server | Search HuggingFace inside the app → pick quantization → download → chat |

The decisive Ollama advantage for builders: **switching your existing OpenAI-client code from cloud to local is often a one-line change** to the `base_url`.

## Production Serving: vLLM and SGLang

When you serve many users concurrently, the bottleneck stops being the model and becomes the **KV cache** — the per-conversation memory that stores attention state. Two engines attack this differently.

### vLLM — PagedAttention

Classic transformer serving stores each conversation's KV cache as one **contiguous chunk of VRAM**. That wastes huge amounts of GPU memory (over-allocate for longest possible sequence, leave gaps when conversations end).

**PagedAttention** treats KV cache like **virtual memory in an OS** — fixed-size blocks, paged on demand. The result: dramatically higher batch sizes and concurrency on the same hardware. vLLM is the default choice for "we need to serve this model to many users."

### SGLang — RadixAttention

For **RAG and shared-context workloads** (many users asking different questions about the same long document), recomputing the document's KV cache per request is wasteful.

**RadixAttention** caches prompt prefixes in a **radix tree** — when a new request shares its first N tokens with an existing one, SGLang reuses the cached KV blocks instead of re-encoding. Berkeley LMSYS team's project; **powers xAI and DeepSeek** in production.

**When to pick which:**
- General high-throughput serving → **vLLM** (more mature, broader hardware support)
- Heavy shared-prefix workloads (RAG over the same documents, system prompts, few-shot examples reused across requests) → **SGLang**

## Apple Silicon: MLX-LM and Unified Memory

A traditional PC has a **hard split**: CPU RAM and GPU VRAM are physically separate. If your model is 24 GB and your GPU has 16 GB VRAM, you're stuck — even if you have 128 GB of system RAM sitting idle.

Apple's M-series chips use **Unified Memory** — one pool of high-speed RAM shared by CPU and GPU. A **Mac Studio with 192 GB** can load models that would otherwise require **4–5 A100 GPUs** at far higher cost and power draw.

**MLX-LM** is Apple's own ML researchers' framework optimized for this architecture. For anyone running the largest open models on a single desktop, MLX on a high-RAM Mac is currently the best price/performance/desk-footprint tradeoff available.

## Decision Heuristic

A simple flow for picking a tool:

1. **Do you need to serve multiple concurrent users?** → vLLM (general) or SGLang (RAG/shared prefix)
2. **Are you on a high-RAM Mac and want the biggest model that fits?** → MLX-LM
3. **Are you building an app and want a drop-in for OpenAI's API?** → Ollama
4. **Do you want a GUI to browse and chat?** → LM Studio
5. **Edge device, embedded, or custom hardware?** → llama.cpp directly

## See Also

- [cpu-gpu-tpu.md](cpu-gpu-tpu.md) — the hardware-architecture layer that determines what fits where
- [ml-systems-at-scale.md](ml-systems-at-scale.md) — production ML serving patterns at large scale
- [../concepts/llm-cost-and-routing.md](../concepts/llm-cost-and-routing.md) — cloud cost models and router patterns (the *other* answer to API cost)
- [../concepts/genai-system-design.md](../concepts/genai-system-design.md) — broader GenAI architecture choices
- [../concepts/rag.md](../concepts/rag.md) — the workload pattern that makes SGLang's RadixAttention shine

---

**Source:** User-shared notes (article + NotebookLM tool-matrix infographic)
**Date:** 2026-06-14
**Tags:** local-llm, inference, ollama, lm-studio, llama-cpp, gguf, vllm, sglang, mlx-lm, paged-attention, radix-attention, apple-silicon, unified-memory, quantization, self-hosted-llm, ai-sovereignty
