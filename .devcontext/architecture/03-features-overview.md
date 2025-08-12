## Transformers features overview (DDD lens)

This document enumerates primary use cases and features exposed by the repository, grouping them into high-level domains in the sense of domain-driven design (DDD). It focuses on what the application can do (capabilities) and who benefits (primary stakeholders).

### Domain: Inference and Serving
- **Feature: Task pipelines (multimodal inference)**
  - Summary: High-level `pipeline(...)` API for inference across text, audio, vision, video, and multimodal tasks. Handles preprocessing, model invocation, and postprocessing.
  - Stakeholders: Application engineers, data scientists, product teams.
  - Value: Rapid prototyping, production-friendly consistent interface, hardware-aware execution.
  - Key docs: `README.md` Quickstart; `docs/source/en/pipeline_tutorial.md`.

- **Feature: Text generation (LLMs/VLMs)**
  - Summary: `generate(...)` with `GenerationConfig` for decoding strategies (greedy, sampling, beam, constrained, streaming). Quantization and device mapping for large models.
  - Stakeholders: Conversational AI teams, content generation, agents.
  - Value: Flexible decoding, performance tuning, reproducible generation configs.
  - Key docs: `docs/source/en/llm_tutorial.md`; generation guides.

- **Feature: CLI chat and utilities**
  - Summary: Command-line chat with models and environment reporting; simplifies local experimentation and diagnostics.
  - Stakeholders: DevOps, ML engineers.
  - Value: Zero/minimal code usage, reproducible runs.
  - Key docs: `README.md` (chat snippet), `src/transformers/commands`.

### Domain: Training and Evaluation
- **Feature: Trainer (PyTorch)**
  - Summary: End-to-end training/evaluation loop with `TrainingArguments`, distributed training (Accelerate), mixed precision, compilation, checkpointing, metrics, Hub integration.
  - Stakeholders: ML researchers, training engineers.
  - Value: Consistent, battle-tested training loop; easy extension via hooks/overrides.
  - Key docs: `docs/source/en/trainer.md`.

- **Feature: Keras.fit (TensorFlow) and Flax support**
  - Summary: Models integrate with native TF/Flax training APIs, enabling cross-backend workflows.
  - Stakeholders: TF/JAX ecosystems.
  - Value: Flexibility of backend choice, cross-framework parity.
  - Key docs: `docs/source/en/philosophy.md` (design goals), TF/Flax docs.

### Domain: Model and Artifact Lifecycle
- **Feature: Hub integration (push/pull, cards, caching)**
  - Summary: `from_pretrained`, `save_pretrained`, `push_to_hub` across models, configs, tokenizers/processors. Automatic caching and versioned artifacts.
  - Stakeholders: Platform teams, collaboration.
  - Value: Reproducibility, sharing, governance.
  - Key docs: Philosophy (main concepts), Hub docs; code in `src/transformers/utils/hub.py`.

- **Feature: Auto registries**
  - Summary: `AutoModel*`, `AutoConfig`, `AutoTokenizer` for dynamic class resolution from configs/identifiers.
  - Stakeholders: Engineers integrating many models.
  - Value: Pluggability and reduced boilerplate.
  - Key files: `src/transformers/models/auto/*`.

### Domain: Data Preparation and Pre/Post-processing
- **Feature: Tokenizers and processors**
  - Summary: Slow and Fast tokenizers, image/audio feature extractors, multimodal processors; standardized preprocessing.
  - Stakeholders: Data/ML engineers.
  - Value: Consistency, performance (Fast tokenizers), modality coverage.
  - Key files: `src/transformers/tokenization_utils*.py`, main_classes docs.

- **Feature: Batch and streaming inference**
  - Summary: Pipelines support batching and streaming inputs; device selection and device_map for multi-device setups.
  - Stakeholders: Serving/inference teams.
  - Value: Throughput/latency tuning.
  - Key docs: `pipeline_tutorial.md`.

### Domain: Optimization and Deployment Readiness
- **Feature: Quantization and memory optimization**
  - Summary: Integration with bitsandbytes and other backends; `device_map="auto"` for big-model inference; dtype control.
  - Stakeholders: Infra/production teams.
  - Value: Reduced memory footprint, cost efficiency.
  - Key docs: `llm_tutorial.md` (quantization, device_map); quantization docs.

- **Feature: Export and interoperability (ONNX, others)**
  - Summary: ONNX conversion paths and runtime guards for cross-runtime compatibility.
  - Stakeholders: Platform and deployment teams.
  - Value: Targeting heterogeneous serving stacks.
  - Key files: `src/transformers/onnx/*`.

### Domain: Observability and Diagnostics
- **Feature: Environment and version reporting**
  - Summary: CLI tooling to report installed versions, devices, optional deps; runtime capability checks.
  - Stakeholders: Support, DevOps.
  - Value: Faster troubleshooting and reproducibility.
  - Key files: `src/transformers/commands/env.py`.

### Domain: Modalities and Tasks (Capability Catalog)
- **Text**: classification, token classification (NER), QA, summarization, translation, text-generation, text2text.
- **Vision**: image classification, segmentation, detection, depth estimation, image-to-text, image-to-image.
- **Audio**: ASR, audio classification, text-to-audio/speech.
- **Video**: video classification.
- **Multimodal**: VQA, document QA, zero-shot variants, image-text-to-text.
- References: `README.md` examples; `pipelines` API list.

### Stakeholders
- **ML researchers**: explore architectures, reproduce papers, fine-tune models, inspect internals, publish artifacts.
- **Training engineers (ML engineers)**: scale training, distributed/accelerated runs, checkpointing, evaluation, Hub CI.
- **Application/product engineers**: integrate inference, chat, pipelines, batching/streaming, API surface stability.
- **Data engineers**: build preprocessing pipelines, dataset streaming, tokenizer/processor management.
- **Platform/infra/DevOps**: deploy models, optimize memory/latency, export (ONNX), environment diagnostics.
- **Governance/compliance**: model cards/metadata, versioning, provenance on the Hub.

### Bounded contexts by lifecycle phase
- **Model Lifecycle**
  - **Primary stakeholders**: researchers, governance, platform teams
  - **Core capabilities**: `from_pretrained`/`save_pretrained`/`push_to_hub`, Auto registries, versioning, model cards
  - **Key artifacts**: `config.json`, weights, `tokenizer.json`, `generation_config.json`, model card
  - **Cross‑cutting deps**: Hub auth, caching

- **Training & Evaluation**
  - **Primary stakeholders**: researchers, training engineers
  - **Core capabilities**: `Trainer` + `TrainingArguments`, metrics, callbacks, distributed (Accelerate), TF/Flax training
  - **Key artifacts**: checkpoints, logs, best‑model selection
  - **Cross‑cutting deps**: PyTorch/TF/Flax, Accelerate, datasets

- **Inference & Serving**
  - **Primary stakeholders**: application engineers, platform teams
  - **Core capabilities**: `pipeline(...)`, `model.generate(...)`, CLI chat, batching/streaming, device mapping
  - **Key artifacts**: generation configs, serialized preprocessors
  - **Cross‑cutting deps**: tokenizers/processor, hardware backends

- **Data Preparation & Pre/Post‑processing**
  - **Primary stakeholders**: data engineers, ML engineers
  - **Core capabilities**: tokenizers (Fast/slow), image/audio processors, multimodal `Processor`
  - **Key artifacts**: vocab files, special tokens, processor configs
  - **Cross‑cutting deps**: `tokenizers`, PIL/Librosa/TorchVision as applicable

- **Optimization & Packaging**
  - **Primary stakeholders**: platform/infra, application engineers
  - **Core capabilities**: quantization (bitsandbytes and others), dtype control, `device_map="auto"`, ONNX export
  - **Key artifacts**: quantization configs, exported graphs
  - **Cross‑cutting deps**: bitsandbytes, onnx/onnxruntime, Accelerate

- **Observability & Diagnostics**
  - **Primary stakeholders**: DevOps, support
  - **Core capabilities**: environment reporting CLI, structured logging hooks
  - **Key artifacts**: env reports, logs
  - **Cross‑cutting deps**: optional‑dependency probes, logging

### Dependencies and extras by bounded context
- **Model Lifecycle**
  - Required: `transformers`, backend (`torch` or `tensorflow` or `flax`), `huggingface_hub`, `safetensors`
  - Optional: `tokenizers` (Rust fast tokenizers, usually installed by default), `datasets` (for Hub dataset streaming)
  - Install hints: `pip install "transformers[torch]"` or `"transformers[tf]"` or `"transformers[flax]"`

- **Training & Evaluation**
  - Required: `transformers`, backend (`torch` recommended for `Trainer`; or TF/Flax), `accelerate`
  - Common optional: `datasets`, `evaluate`, `deepspeed` (large-scale PT), `peft` (parameter‑efficient fine‑tuning), `bitsandbytes` (quantized fine‑tuning/inference), `sentencepiece` (for some tokenizers)
  - Install hints: `pip install "transformers[torch] accelerate datasets"`

- **Inference & Serving**
  - Required: `transformers`, backend (`torch`/`tf`/`flax`)
  - Common optional: `accelerate` (`device_map="auto"`), `bitsandbytes` (memory‑efficient inference), `tokenizers`, modality deps: `timm`/`torchvision`/`Pillow` (vision), `librosa`/`torchaudio` (audio), `av` (video)
  - Install hints: `pip install "transformers[torch] accelerate bitsandbytes pillow"`

- **Data Preparation & Pre/Post‑processing**
  - Required: `tokenizers`
  - Modality optional: `Pillow` (images), `librosa`/`torchaudio` (audio), `torchvision` (vision transforms)
  - Dataset optional: `datasets`

- **Optimization & Packaging**
  - Quantization optional: `bitsandbytes`, `auto_gptq`, `awq`, `torchao`, `autoround`
  - Export optional: `onnx`, `onnxruntime`
  - Install hints: `pip install onnx onnxruntime`, `pip install bitsandbytes`

- **Observability & Diagnostics**
  - Optional: `coloredlogs` (nicer CLI logs)
  - Built‑in: `transformers` logging utilities, environment command

### Ubiquitous language (shared glossary)
- **pipeline**: High-level inference orchestrator that wires preprocessing, model invocation, and postprocessing for a named task (e.g., "text-generation").
- **task**: A named capability the pipeline supports (e.g., text-classification, image-segmentation, ASR, VQA).
- **pretrained model**: A model class plus weights loaded via `from_pretrained(...)` (often from the Hub).
- **configuration (PretrainedConfig)**: JSON‑serializable hyperparameters and metadata used to construct a model; separate from weights.
- **auto classes**: Factory mappers (`AutoConfig`, `AutoModel*`, `AutoTokenizer`) that resolve concrete classes from identifiers/config.
- **tokenizer**: Text preprocessor that maps strings to token IDs and back. "Fast" tokenizers are Rust‑backed; "slow" are Python.
- **processor**: Unified wrapper for multimodal preprocessing that may include tokenizers, image/audio processors.
- **feature extractor / image processor**: Modality‑specific preprocessors for audio and vision inputs, respectively.
- **ModelOutput**: Standardized, dict‑like return object with named fields (e.g., `logits`, `loss`, `hidden_states`).
- **generation (generate)**: Decoding API to produce sequences from generative models; controlled by `GenerationConfig` or function args.
- **GenerationConfig**: Serializable decoding parameters (e.g., `max_new_tokens`, `do_sample`, `num_beams`, stopping tokens).
- **device map**: Strategy (often via Accelerate) to shard and place model weights across available devices; `device_map="auto"` selects automatically.
- **quantization**: Numeric precision reduction of weights/activations to lower memory and speed up inference (e.g., via bitsandbytes, GPTQ).
- **checkpoint**: Saved training state (model weights, optionally optimizer/scheduler state) at a specific step/epoch.
- **Trainer / TrainingArguments**: High‑level training loop and configuration for PyTorch models with built‑in evaluation, logging, saving, and Hub integration.
- **Hub**: Central registry for versioned models/datasets/spaces; used by `from_pretrained`, `push_to_hub`, caching.
- **from_pretrained / save_pretrained / push_to_hub**: Lifecycle methods to load, persist, and publish models/configs/tokenizers/processors.
