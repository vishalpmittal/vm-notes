# GenAI Project Structure

A reference directory layout for a production Generative AI / LLM application, organized for scalability, maintainability, and easy integration of new models and components.

## Key Takeaways

- **Separate concerns by directory**: config, data artifacts, source code, and operational scripts each get their own top-level home so the codebase stays navigable as it grows
- **Keep model/provider choices in config, not code** — a `model_config.yaml` lets you swap LLM providers, models, and parameters without touching source
- **Treat generated data as disposable artifacts** — caches, embeddings, and vector-DB indexes live under `data/` (and belong in `.gitignore`), regenerable via scripts
- **Abstract the LLM behind a `core/` layer** so GPT, Claude, or open-weight models are interchangeable behind one interface
- **Make it reproducible from day one** — Dockerfile, `requirements.txt`, and setup/build scripts so the project spins up and scales the same way everywhere

## Directory Layout

```
genai-project/
├── .gitignore              # exclude caches, embeddings, secrets from version control
├── Dockerfile              # containerized build for consistent deploy/scale
├── docker-compose.yml      # local multi-service orchestration
├── requirements.txt        # pinned dependencies for reproducible setup
├── config/
│   ├── model_config.yaml   # LLM providers, model names, parameters
│   └── logging_config.yaml # log setup + levels for traceability/debugging
├── data/
│   ├── cache/              # cached responses and intermediates
│   ├── embeddings/         # vector embeddings generated from models
│   └── vectordb/           # vector index (FAISS, Chroma, …) for retrieval
├── src/
│   ├── core/              # base LLM abstractions (GPT / Claude / … integration)
│   ├── prompts/           # reusable prompt templates + multi-step chain logic
│   ├── rag/               # retrieval-augmented generation: retrieval + indexing
│   ├── processing/        # text chunking, tokenization, preprocessing utilities
│   └── inference/         # inference orchestration, output parsing, formatting
└── scripts/
    ├── setup_env.sh        # environment setup for seamless execution
    ├── run_tests.sh        # automated test runner
    ├── build_embeddings.py # generate embeddings for project data
    └── cleanup.py          # remove unused data / temp files
```

## What Each Piece Does

**Project root** — reproducibility and deployment surface. `.gitignore` keeps generated artifacts and secrets out of version control; `Dockerfile` + `docker-compose.yml` make deploy/scale consistent; `requirements.txt` pins dependencies.

**`config/`** — externalized configuration. `model_config.yaml` defines LLM providers, models, and parameters so they can change without code edits; `logging_config.yaml` handles logging setup and levels for traceability and debugging.

**`data/`** — generated, regenerable artifacts (all gitignored). `cache/` for responses and intermediates, `embeddings/` for model-generated vectors, `vectordb/` for the vector index (FAISS, Chroma) used in retrieval.

**`src/`** — application code:
- `core/` — base abstractions over LLMs so different models are swappable behind one interface
- `prompts/` — reusable prompt templates and chain logic for multi-step execution
- `rag/` — retrieval-augmented generation components (document retrieval + indexing)
- `processing/` — chunking, tokenization, and data preprocessing utilities
- `inference/` — inference orchestration, output parsing, and formatting

**`scripts/`** — operational automation: environment setup, test runs, embedding builds, and cleanup of stale data/temp files.

## Related

- [genai-system-design.md](genai-system-design.md) — the architecture this layout implements (model families, deployment tiers, data pipeline)
- [rag.md](rag.md) — what goes inside `src/rag/`
- [vector-databases.md](vector-databases.md) — the `data/vectordb/` layer
- [context-engineering.md](context-engineering.md) — informs `src/prompts/` and retrieval design
- [ai-engineering-discipline.md](ai-engineering-discipline.md) — why generated code/data is treated as a disposable cache

---

**Source:** Imported from pasted notes
**Date:** 2026-07-29
**Tags:** genai, llm, project-structure, repository-layout, scalability, rag, vector-database, mlops, engineering-practices
