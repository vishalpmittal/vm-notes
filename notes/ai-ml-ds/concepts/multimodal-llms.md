# Multimodal LLMs: How One Model Handles Text + Image + Audio + Video

The unifying trick: convert every modality into the same embedding space, then let the language model backbone reason over them uniformly.

## Key Takeaways

- **Multimodal LLMs unify modalities into a shared embedding space.** Text, images, audio — all get projected into the same vector space where the language model operates
- **Three-part architecture**: modality-specific encoders → projection layers → shared language model backbone. Add a new modality by adding a new encoder + projection
- **Vision Transformers** process images as "visual sentences" — divide into patches, treat each patch as a token, run through transformer attention. Same architecture as text LLMs
- **Audio becomes images** — convert audio waves to spectrograms (2D frequency representations), then process with the same vision approach
- **CLIP** (OpenAI, 400M image-text pairs, contrastive learning) is the canonical alignment example — the trained model places matching images and text close together in the shared space

## The Core Trick: A Shared Embedding Space

A text LLM processes tokens. A multimodal LLM processes "tokens" too — but those tokens come from different sources:

```
text:    "a cat"           ──tokenizer──>    [token1, token2]
image:   <photo of cat>    ──vision encoder──>  [patch1, patch2, patch3...]
audio:   <meow sound>      ──audio encoder──>   [audio_token1, audio_token2...]
```

All three end up as vectors in the same space. The language model backbone (GPT, LLaMA, Claude — pick your transformer) operates on these vectors identically.

> "Every type of input, whether text, images, or audio, gets converted into the same type of mathematical representation."

The model doesn't have separate "vision logic" and "text logic" — it has one logic over a shared representation.

## Architecture: Three Layers

### 1. Modality-Specific Encoders

Each modality needs its own encoder to produce vectors:

**Vision (Vision Transformers, ViT):**
- Divide the image into small patches (e.g., 16×16 pixels)
- Linearly project each patch into a vector — same as token embedding
- Run through standard transformer attention
- Result: each image patch is a "token"

**Audio:**
- Convert waveform into a **spectrogram** — a 2D frequency-vs-time representation
- Treat spectrogram chunks as image patches
- Use the same vision-encoder approach

**Video:**
- Treat as sequences of image frames (each frame encoded as vision tokens)
- Add temporal attention across frames

**Other modalities** (3D, sensor data, etc.) — same pattern: encode → produce vectors → feed to the language model.

### 2. Projection Layers

The encoders produce vectors, but their geometry doesn't naturally match the language model's embedding space. **Projection layers** (often simple linear transformations) align them.

After projection, a "cat image patch" vector and a "cat" token vector live in compatible coordinates — the language model can reason over both as if they were the same modality.

### 3. Language Model Backbone

The reasoning engine — typically a transformer (GPT, LLaMA, Mistral). Receives the unified token sequence and runs standard attention/feedforward on them.

The backbone doesn't need to know which tokens came from text, images, or audio. The architecture is **modality-agnostic at the reasoning layer**.

## CLIP — The Canonical Alignment

CLIP (Contrastive Language-Image Pre-training, OpenAI 2021) trained on **400M image-text pairs** using contrastive learning:

```
Image of a dog + caption "a dog"    → maximize similarity in embedding space
Image of a dog + caption "a cat"    → minimize similarity
```

After training, CLIP can:
- Take any image, return the most similar caption from a candidate set (zero-shot classification)
- Take any text, return matching images from a corpus (semantic image search)
- Serve as the **vision encoder** for many later multimodal LLMs (DALL-E uses CLIP guidance; Stable Diffusion uses CLIP text encoding)

CLIP is also the reason "describe this image" works — the trained alignment lets text and image flow through the same understanding pipeline.

## Training Stages

Multimodal models typically train in two stages:

### Stage 1: Alignment
Freeze the pre-trained components (already-trained vision encoder, already-trained language model). Train **only the projection layers** to align modalities.

Why freeze? Pre-trained backbones are expensive to retrain and already capable. Only the connectors need to learn the modality-mapping.

### Stage 2: Instruction Tuning
Unfreeze (or partially unfreeze) and fine-tune on **multimodal instruction data** — often synthetically generated. Example: GPT-4-generated conversations about images train the model to follow user instructions about visual content.

Result: a model that can answer "what's in this image and how should I improve it?" not just classify images.

## Why "Multimodal" Is Now the Default

Modern frontier models are multimodal by default:
- **GPT-4o / GPT-5** — native multimodal (text + image + audio)
- **Claude** — vision + text
- **Gemini** — text + image + audio + video
- **Qwen2.5-VL** — open-weight multimodal

The reason: multimodal capability massively expands what the model can do without much extra architecture cost. You add the encoder + projection; the backbone is the same. The marginal cost of adding modalities decreased as the unified-embedding approach matured.

## What This Doesn't Solve

- **Modality-specific reasoning differences** — a model is good at images iff its image encoder + training data are good; same for audio. Adding the encoder doesn't automatically make the model an audio expert
- **Cross-modal grounding gaps** — the model may have aligned features but still fail at tasks requiring deep cross-modal reasoning (e.g., counting objects in an image while reasoning about their colors)
- **Generation across modalities** — generating an image from text (e.g., DALL-E, Stable Diffusion) is a different problem; see [image-generation-diffusion.md](image-generation-diffusion.md)

## Generalizable Pattern: Unified Representation Spaces

The "embed everything into a shared space, then reason uniformly" pattern goes beyond multimodal LLMs:

- **Vector databases** apply the same idea to documents, products, songs — anything embeddable becomes searchable in the same space (see [vector-databases.md](vector-databases.md))
- **Multi-language embeddings** put English, French, Chinese into the same space (see [transformer-architecture.md § multilingual](transformer-architecture.md))
- **Multi-modal recommendations** use the same approach to recommend across product types

If you can find a meaningful embedding, the language model can reason over it.

## Related

- [Transformer architecture](transformer-architecture.md) — the substrate that makes this work
- [Image generation: diffusion](image-generation-diffusion.md) — the generation side of multimodality
- [Vector databases](vector-databases.md) — same shared-embedding-space idea applied to retrieval
- [AI trends 2026 § multimodal](ai-trends-2026.md) — the bifurcation into Physical AI and World Models
- [AI glossary § embeddings](ai-glossary.md#6-embeddings) — quick reference

---

**Source:** https://blog.bytebytego.com/p/multimodal-llms-basics-how-llms-process
**Date:** 2026-06-05
**Tags:** multimodal-llms, vision-transformer, vit, clip, embeddings, modality-encoder, projection-layer, gpt-4o, contrastive-learning, instruction-tuning
