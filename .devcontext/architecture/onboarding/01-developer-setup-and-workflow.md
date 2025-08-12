## Developer setup and workflow

This guide helps new contributors get a local environment ready for development, run quick checks, and iterate efficiently. It also serves as input for a Dockerfile/devcontainer design.

### Prerequisites
- Python 3.9+ (recommended: 3.10/3.11)
- git, build tools (C/C++ toolchain), and a virtual environment tool (`venv` or `uv`)
- Optional GPU backends
  - NVIDIA CUDA for PyTorch
  - Apple Silicon (`mps`) on macOS 13+

References: `README.md`, `docs/source/en/index.md`.

### Clone and environment
```bash
git clone https://github.com/huggingface/transformers.git
cd transformers

# venv (Python stdlib)
python -m venv .venv && source .venv/bin/activate

# or: uv (fast Python manager)
uv venv .venv && source .venv/bin/activate
```

### Install (choose one backend, add extras as needed)
- PyTorch (typical):
```bash
pip install -U "transformers[torch]" accelerate datasets
# Editable install for local dev against this repo:
pip install -e .[torch]
```

- TensorFlow:
```bash
pip install -U "transformers[tf]"
```

- Flax/JAX:
```bash
pip install -U "transformers[flax]"
```

Optional extras (install only if needed):
- Memory-efficient inference: `bitsandbytes`
- Quantization backends: `auto_gptq`, `awq`, `torchao`, `autoround`
- Vision/audio: `Pillow`, `torchvision`, `librosa`, `torchaudio`, `av`

Reference: `src/transformers/utils/import_utils.py` (optional-deps detection).

### Verify environment
Run the CLI environment report to confirm versions and hardware detection.
```bash
python -m transformers.commands.env | cat
# or
transformers env | cat
```
Reference: `src/transformers/commands/env.py`.

### Quick inference sanity check (Pipeline)
```python
from transformers import pipeline

pipe = pipeline(task="text-generation", model="Qwen/Qwen2.5-0.5B")
print(pipe("hello world "))
```
Notes:
- Use `device=0` for GPU, or `device="mps"` on Apple Silicon
- For very large models, consider `device_map="auto"` (requires `accelerate`)

References: `docs/source/en/pipeline_tutorial.md`, `src/transformers/pipelines/__init__.py`.

### Quick training sanity check (Trainer)
```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer, Trainer, TrainingArguments
from datasets import load_dataset

model_id = "distilbert/distilbert-base-uncased-finetuned-sst-2-english"
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForSequenceClassification.from_pretrained(model_id)

dataset = load_dataset("glue", "sst2")
def tok(batch):
    return tokenizer(batch["sentence"], truncation=True)
tokenized = dataset.map(tok, batched=True)

args = TrainingArguments(output_dir="./tmp", per_device_train_batch_size=8, per_device_eval_batch_size=8, num_train_epochs=1)
trainer = Trainer(model=model, args=args, train_dataset=tokenized["train"].select(range(256)), eval_dataset=tokenized["validation"].select(range(256)))
trainer.train()
```
References: `docs/source/en/trainer.md`.

### Running tests
- Install test extras (example subsets exist in `examples/*/_tests_requirements.txt`).
```bash
pip install -r examples/pytorch/_tests_requirements.txt || true
pytest -q tests -k pipelines | cat
```
- To limit scope, filter by directory or `-k` expression (e.g., `generation`, `trainer`).

### Running examples
- Example directories: `examples/pytorch/*`, `examples/tensorflow/*`, `examples/flax/*`.
- Most example READMEs include prerequisites and command lines.

### Style, linting, and conventions
- Match existing formatting in touched files; prefer multi-line readability.
- Follow Python naming guidance (descriptive names; avoid cryptic abbreviations).
- If `pre-commit`/linters are configured locally, run them before commits; otherwise keep diffs minimal and scoped.

### Useful scripts
- Tokenizers check: `scripts/check_tokenizers.py`
- Distributed GPU sanity test: `scripts/distributed/torch-distributed-gpu-test.py`

### Troubleshooting
- Print environment with `transformers env` and confirm optional deps (e.g., `bitsandbytes`, `timm`).
- For large models, use `device_map="auto"` and quantization.
- See `.devcontext/architecture/onboarding/troubleshooting-guide.md`.

### Notes for Dockerfile/devcontainer design
- Base image: match Python and CUDA (if using GPU). Install system build deps (e.g., git, gcc/g++, make). 
- Layered installs:
  - Core: `transformers` + chosen backend (`[torch]` or `[tf]` or `[flax]`)
  - Dev: `accelerate datasets evaluate pytest`
  - Optional by context: `bitsandbytes Pillow torchvision librosa torchaudio av onnx onnxruntime`
- Cache huggingface directories (`HF_HOME`) between builds where possible.
- Include a non-root user for local dev; mount the repo to enable editable installs (`pip install -e .`).

### Pointers for deeper dives
- High-level architecture: `.devcontext/architecture/02-architectural-research.md`
- Features and DDD view: `.devcontext/architecture/03-features-overview.md`
- Pipelines tutorial: `docs/source/en/pipeline_tutorial.md`
- Trainer guide: `docs/source/en/trainer.md`
- Generation tutorial: `docs/source/en/llm_tutorial.md`


### Devcontainer recommendations (CUDA and Apple Silicon)
- Maintain two variants:
  - CUDA (linux/amd64) for Linux hosts with NVIDIA GPUs
  - ARM64 (linux/arm64) for Apple Silicon (container will be CPU-only; use host Python for MPS)
- Selection and runtime:
  - CUDA: add `--gpus all` (requires NVIDIA Container Toolkit)
  - Apple Silicon: no GPU flags; MPS is not exposed inside Linux containers
- Consider multi-arch builds with `buildx`, or keep separate Dockerfiles.

Recommended devcontainer presets

CUDA (linux/amd64):
```json
{
  "name": "transformers-cuda",
  "build": { "dockerfile": "cuda.Dockerfile", "context": ".." },
  "runArgs": ["--gpus", "all"],
  "remoteUser": "vscode"
}
```

Apple Silicon (linux/arm64):
```json
{
  "name": "transformers-arm64",
  "build": { "dockerfile": "arm64.Dockerfile", "context": "..", "args": { "TARGETPLATFORM": "linux/arm64" } },
  "remoteUser": "vscode"
}
```

Base Dockerfiles

`cuda.Dockerfile`:
```Dockerfile
FROM nvidia/cuda:12.1.1-cudnn8-runtime-ubuntu22.04
RUN apt-get update && apt-get install -y \
    python3 python3-venv python3-pip git build-essential \
    && rm -rf /var/lib/apt/lists/*
RUN pip install -U "transformers[torch]" accelerate datasets
```

`arm64.Dockerfile`:
```Dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y \
    python3 python3-venv python3-pip git build-essential \
    && rm -rf /var/lib/apt/lists/*
RUN pip install -U "transformers[torch]" accelerate datasets
```

Usage guidance
- Linux + NVIDIA: use CUDA container (`--gpus all`) for GPU training/inference
- macOS Apple Silicon: use ARM64 container for tooling; for GPU acceleration, run Python on host with `device="mps"` or use a remote Linux GPU container/server

