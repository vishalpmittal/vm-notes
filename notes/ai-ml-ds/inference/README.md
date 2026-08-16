# ai-ml-ds/inference — ML serving, inference at scale, and AI hardware

Notes on running trained models efficiently in production: serving platforms, inference optimization, real-time/voice infrastructure, and the underlying compute hardware. Model architecture and design patterns live in concepts/; agent runtimes live in agents/.

## Notes
- [ai-infrastructure.md](ai-infrastructure.md) — GPU data center as a constrained physical system (power, cooling, network)
- [cpu-gpu-tpu.md](cpu-gpu-tpu.md) — architectural differences and workload-to-hardware fit
- [inference-engineering.md](inference-engineering.md) — prefill/decode split and six core inference optimizations
- [local-llm-inference.md](local-llm-inference.md) — running LLMs locally (llama.cpp, Ollama, vLLM, MLX, quantization)
- [lyftlearn-serving.md](lyftlearn-serving.md) — Lyft's per-team microservices ML serving platform
- [ml-systems-at-scale.md](ml-systems-at-scale.md) — where cost lives at scale; feature stores and train-serve skew
- [real-time-ai-interaction.md](real-time-ai-interaction.md) — micro-turn voice architecture vs stacked VAD/STT/LLM/TTS harness
- [voice-ai-production-infra.md](voice-ai-production-infra.md) — OpenAI's WebRTC relay/transceiver voice architecture and ICE routing
