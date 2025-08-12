## Document review: overlap between architecture guide and existing docs

This review highlights where content in `.devcontext/architecture/*` duplicates existing documentation under `docs/source/en/`. Aim to minimize duplication by linking to canonical docs and focusing the architecture guide on: system mental model (C4), DDD domains/bounded contexts, cross-cutting decisions (ADRs), and repo-specific onboarding.

### 1) High-level overview and features
- **Architecture file**: `03-features-overview.md` (capabilities: Pipelines, Trainer, generate, Hub, Auto classes).
- **Docs already cover**: `docs/source/en/index.md` (Features + Design), `pipeline_tutorial.md` (Pipelines), `trainer.md` (Trainer), `llm_tutorial.md` (Text generation).
- **Notes**: Keep the DDD grouping and stakeholder mapping; link to the above docs for feature details to avoid restating usage and parameters.

### 2) Foundational abstractions and main classes
- **Architecture file**: `02-architectural-research.md` (PretrainedConfig, PreTrainedModel, Tokenizers, ModelOutput, Auto registries).
- **Docs already cover**:
  - Configuration: `main_classes/configuration.md`
  - Models: `main_classes/model.md`
  - Tokenizers: `fast_tokenizers.md`, `main_classes/tokenizer.md`, `tokenizer_summary.md`
  - Auto classes: `model_doc/auto.md`
  - Generation APIs: `main_classes/text_generation.md`
- **Notes**: Retain the quick index of key files and architecture rationale; replace concept explanations with links to the main-class docs.

### 3) Pipelines workflow
- **Architecture artifact**: `diagrams/sequence-pipeline-inference.mmd` and references in `03-features-overview.md`.
- **Docs already cover**: `pipeline_tutorial.md` (end-to-end flow, parameters, batching/streaming, device selection).
- **Notes**: Keep the compact sequence diagram for onboarding; link to the tutorial for authoritative details.

### 4) Trainer training loop
- **Architecture artifact**: `diagrams/sequence-trainer-training-loop.mmd`; `03-features-overview.md` Training section.
- **Docs already cover**: `trainer.md` (loop, checkpoints, logging, customization, Accelerate, optimizations).
- **Notes**: Keep the sequence diagram; remove repeated step-by-step narrative and link to `trainer.md` for specifics.

### 5) Hub lifecycle (from_pretrained/save_pretrained/push_to_hub)
- **Architecture files**: `03-features-overview.md` (Lifecycle), `02-architectural-research.md` (Hub and I/O), `diagrams/sequence-hub-from_pretrained-and-push_to_hub.mmd`.
- **Docs already cover**: `philosophy.md` (overview), `model_sharing.md` (uploading/pushing), `main_classes/model.md` (PushToHubMixin), plus inline sections in `llm_tutorial.md` and `index.md`.
- **Notes**: Keep lifecycle framing and the sequence diagram; link to Hub docs for workflows and parameters.

### 6) Generation and GenerationConfig
- **Architecture files**: `02-architectural-research.md` (GenerationConfig separation), `03-features-overview.md` (Text generation domain).
- **Docs already cover**: `llm_tutorial.md`, `main_classes/text_generation.md`, `generation_strategies.md`.
- **Notes**: Keep the architectural recommendation (prefer `GenerationConfig`); link to docs for API usage and options.

### 7) Tokenizers and processors
- **Architecture files**: `02-architectural-research.md` (Tokenizers/Processor), `03-features-overview.md` (Data preparation domain).
- **Docs already cover**: `fast_tokenizers.md`, `main_classes/tokenizer.md`, `processors.md`.
- **Notes**: Avoid repeating tokenizer classes and training guidance; link to tokenizer docs and keep only architecture-relevant guidance (e.g., prefer Fast tokenizers).

### 8) Quantization and memory optimization
- **Architecture files**: `03-features-overview.md` (Optimization & Packaging), onboarding `03-optional-dependencies-matrix.md`.
- **Docs already cover**: `quantization/overview.md`, `quantization/selecting.md`, `main_classes/quantization.md`, plus bitsandbytes usage in `llm_tutorial.md`.
- **Notes**: Keep high-level positioning; link to quantization docs for methods/backends tables and flags.

### 9) Export and interoperability (ONNX, others)
- **Architecture files**: `03-features-overview.md` (Export), references to `src/transformers/onnx/*`.
- **Docs already cover**: `serialization.md` (Optimum ONNX export), `main_classes/onnx.md`.
- **Notes**: Link to Optimum exporter docs instead of repeating CLI/API steps.

### 10) CLI usage (chat/env/serve)
- **Architecture files**: `02-architectural-research.md` (CLI & tooling), `03-features-overview.md` (CLI chat), onboarding workflows.
- **Docs already cover**: `conversations.md` (chat CLI), `serving.md` (serve CLI), `index.md` (feature mentions).
- **Notes**: Reference CLI docs; avoid duplicating command options and flags.

### 11) Glossary / ubiquitous language
- **Architecture file**: `03-features-overview.md` (Ubiquitous language list).
- **Docs already cover**: `glossary.md`.
- **Notes**: Either remove duplicated definitions or keep only terms absent from the official glossary; otherwise link to `glossary.md`.

### 12) C4 L1/L2 and sequence diagrams
- **Architecture artifacts**: `diagrams/*.mmd`.
- **Docs already cover**: Not as diagrams, but equivalent narratives exist in `index.md`, `pipeline_tutorial.md`, `trainer.md`, `llm_tutorial.md`.
- **Notes**: Diagrams are additive; keep them, but ensure captions link to the corresponding canonical docs.

### 13) Optional dependencies matrix and troubleshooting
- **Architecture files**: onboarding `03-optional-dependencies-matrix.md`, `06-troubleshooting-guide.md`.
- **Docs already cover**: Scattered across installation/performance/quantization pages, with partial overlap.
- **Notes**: Keep matrices and troubleshooting if they consolidate repo specifics, but replace any repeated install/flag instructions with links to canonical pages.

---

### Recommendations to reduce duplication
- Replace repeated explanations with links to the canonical docs listed above.
- Keep architecture docs focused on: system boundaries (C4), bounded contexts, ADRs, stakeholder mapping, and “how this repo is organized.”
- Where checklists or matrices are valuable (e.g., optional deps), keep them but avoid copying command lines from tutorials; link instead.

### Quick cross-reference (architecture → docs)
- `02-architectural-research.md` → `main_classes/*`, `model_doc/auto.md`, `fast_tokenizers.md`, `philosophy.md`
- `03-features-overview.md` → `index.md`, `pipeline_tutorial.md`, `trainer.md`, `llm_tutorial.md`, `quantization/*`, `serialization.md`
- `diagrams/*` → `index.md`, `pipeline_tutorial.md`, `trainer.md`, `llm_tutorial.md`, `model_sharing.md`
- onboarding `03-optional-dependencies-matrix.md` → `quantization/*`, `installation`/performance pages
- onboarding `05-model-lifecycle-guide.md` → `model_sharing.md`, `main_classes/model.md`


### Net-new content provided by the architecture docs (not in `docs/`)
- **System diagrams (C4 + sequences)**: Concise C4 L1/L2 and key workflow sequences purpose-built for onboarding, stored in `diagrams/*.mmd` with an editing `README.md`. The official docs describe flows but do not provide a compact diagram set.
- **DDD view and bounded contexts**: Domain grouping, stakeholder mapping, and lifecycle framing in `03-features-overview.md` (plus dependencies per bounded context) are not organized this way in the docs.
- **Consolidated support/optional-deps matrices**: `03-optional-dependencies-matrix.md` and `04-backend-support-matrix.md` provide a one-glance view across contexts/backends; the docs have information but it’s dispersed.
- **Repo-centric onboarding**: `01-developer-setup-and-workflow.md` and `02-common-workflows.md` focus on local contributor workflow and common dev tasks, which go beyond end-user docs.
- **Model lifecycle guide (repo-specific)**: `05-model-lifecycle-guide.md` ties artifacts (`config.json`, `generation_config.json`, weights, tokenizers) to Hub workflows in a single narrative tailored to this repo.
- **Troubleshooting consolidation**: `06-troubleshooting-guide.md` aggregates cross-backend/device issues and remediation in one place.
- **Security and licensing orientation**: `08-security-and-licensing.md` contextualizes licensing/security considerations for contributors; the docs do not centralize this view.
- **Architectural decision records (ADRs)**: `adrs/*.md` capture internal decisions (lazy imports, generated pipeline typings, GenerationConfig separation, multi-backend single tree) that are not present in the user docs.
- **Architecture research + code index**: `02-architectural-research.md` links foundational abstractions to concrete files and highlights deviations/practices; complements but does not duplicate docs.
- **Prompted, reproducible authoring**: `prompts/*.prompt.md` codify how to regenerate guides/diagrams consistently inside this repo’s context.
