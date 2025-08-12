## Model lifecycle guide (load → train → evaluate → save → publish → serve)

This short guide outlines the end-to-end model lifecycle in this repo and on the Hub, including key artifacts and minimal commands.

### Lifecycle at a glance
```mermaid
flowchart TB
    A[Select model/checkpoint
    (Hub or local)] --> B[Load config/tokenizer/weights
    from_pretrained]
    B --> C[Train/Evaluate
    Trainer or TF/Flax]
    C --> D[Save artifacts
    save_pretrained]
    D --> E[Publish to Hub
    push_to_hub]
    E --> F[Serve/Infer
    pipeline / generate]
    F --> G[Optional export
    ONNX]
```

### 1) Discover and load
- Find a checkpoint on the Hub or a local path. Loading pulls config, weights, and tokenizer/processors.
```python
from transformers import AutoConfig, AutoTokenizer, AutoModelForCausalLM

model_id = "mistralai/Mistral-7B-v0.1"
config = AutoConfig.from_pretrained(model_id)
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id)
```
References: `src/transformers/models/auto/*`, `docs/source/en/index.md`.

### 2) Train and evaluate
- PyTorch `Trainer` (first-class), or native TF/Flax.
```python
from transformers import Trainer, TrainingArguments

args = TrainingArguments(output_dir="./out", num_train_epochs=1)
trainer = Trainer(model=model, args=args, train_dataset=train_ds, eval_dataset=eval_ds)
trainer.train()
metrics = trainer.evaluate()
```
References: `docs/source/en/trainer.md`, `src/transformers/modeling_utils.py`.

### 3) Save artifacts
- Persist model, tokenizer, and configs for reuse.
```python
model.save_pretrained("./my-model")
tokenizer.save_pretrained("./my-model")
```
Artifacts typically include:
- `config.json` (model hyperparameters)
- Weights (e.g., `pytorch_model.bin` or `model.safetensors`)
- Tokenizer files (e.g., `tokenizer.json`, `special_tokens_map.json`, `tokenizer_config.json`)
- Optional: `generation_config.json` (decoding defaults)
- Optional: README/model card

### 4) Publish to Hub
- Share artifacts; consumers can load with `from_pretrained`.
```python
model.save_pretrained("./my-model", push_to_hub=True)
tokenizer.save_pretrained("./my-model", push_to_hub=True)
```
References: `src/transformers/utils/hub.py`.

### 5) Serve/infer
- Use high-level pipelines or direct `generate`.
```python
from transformers import pipeline

pipe = pipeline("text-generation", model="<your-namespace>/my-model")
print(pipe("Hello ", max_new_tokens=64))
```
References: `docs/source/en/pipeline_tutorial.md`, `docs/source/en/llm_tutorial.md`.

### 6) Optional export (ONNX)
```bash
python -m transformers.onnx --model=<your-namespace>/my-model onnx/
```
References: `src/transformers/onnx/__main__.py`, `src/transformers/onnx/convert.py`.

### Notes and best practices
- Keep `generation_config.json` when shipping generative models to capture default decoding.
- Prefer `safetensors` format for security/performance when available.
- Track library version (`src/transformers/__init__.py` `__version__`) in your model card for reproducibility.
- For large models, consider `accelerate` and quantization (`bitsandbytes`) during inference.

### Related reading
- Philosophy and main concepts: `docs/source/en/philosophy.md`
- High-level design and features: `.devcontext/architecture/02-architectural-research.md`, `.devcontext/architecture/03-features-overview.md`

