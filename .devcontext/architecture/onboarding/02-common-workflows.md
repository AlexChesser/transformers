## Common workflows playbook

This playbook provides minimal, copy‑pastable flows for frequent tasks. See also `.devcontext/architecture/02-architectural-research.md` and `.devcontext/architecture/03-features-overview.md`.

### Prerequisites (choose one backend)
- PyTorch (typical):
```bash
pip install -U "transformers[torch]" accelerate
```
- TensorFlow:
```bash
pip install -U "transformers[tf]"
```
- Flax/JAX:
```bash
pip install -U "transformers[flax]"
```
Optional: `datasets`, `evaluate`, `bitsandbytes`, `Pillow`, `torchvision`, `librosa`, `torchaudio`, `onnx`, `onnxruntime`.

References: `README.md`, `docs/source/en/index.md`, `src/transformers/utils/import_utils.py`.

---

### 1) Run inference with pipelines
```python
from transformers import pipeline

pipe = pipeline(task="text-generation", model="Qwen/Qwen2.5-0.5B")
print(pipe("hello world ", max_new_tokens=30))

# GPU or Apple Silicon
# pipe = pipeline(task="text-generation", model="Qwen/Qwen2.5-0.5B", device=0)    # CUDA GPU
# pipe = pipeline(task="text-generation", model="Qwen/Qwen2.5-0.5B", device="mps")  # Apple Silicon
```
Notes: use `device_map="auto"` with `accelerate` for large models. For batch inputs, pass a list. 

References: `docs/source/en/pipeline_tutorial.md`, `src/transformers/pipelines/__init__.py`, `docs/source/en/llm_tutorial.md`.

---

### 2) Train with Trainer (PyTorch)
```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification, Trainer, TrainingArguments
from datasets import load_dataset

model_id = "distilbert/distilbert-base-uncased-finetuned-sst-2-english"
tok = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForSequenceClassification.from_pretrained(model_id)

ds = load_dataset("glue", "sst2")
ds_tok = ds.map(lambda b: tok(b["sentence"], truncation=True), batched=True)

args = TrainingArguments(output_dir="./out", per_device_train_batch_size=8, per_device_eval_batch_size=8, num_train_epochs=1)
trainer = Trainer(model=model, args=args, train_dataset=ds_tok["train"].select(range(512)), eval_dataset=ds_tok["validation"].select(range(512)))
trainer.train()
```
References: `docs/source/en/trainer.md`, `src/transformers/modeling_utils.py`.

---

### 3) Add a new model (high‑level pointers)
- Start from the template: `templates/adding_a_new_model/README.md`.
- Define:
  - Config: subclass of `PretrainedConfig` (file under `src/transformers/models/<your_model>/configuration_*.py`).
  - Model: subclass of `PreTrainedModel` (PyTorch) or TF/Flax equivalents.
  - (Optional) Tokenizer/Processor if custom preprocessing is required.
- Register in auto mappings so `AutoConfig`/`AutoModel*` work.

References: `templates/adding_a_new_model/`, `src/transformers/models/auto/configuration_auto.py`, `src/transformers/models/auto/modeling_auto.py`, `src/transformers/configuration_utils.py`, `src/transformers/modeling_utils.py`.

---

### 4) Publish to Hub
```python
from transformers import AutoTokenizer, AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("mistralai/Mistral-7B-v0.1")
tok = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-v0.1")

model.save_pretrained("./my-model", push_to_hub=True)
tok.save_pretrained("./my-model", push_to_hub=True)
```
Notes: Authenticate with `huggingface-cli login` first. You can also use `Trainer(..., push_to_hub=True)`.

References: `src/transformers/utils/hub.py`, `docs/source/en/trainer.md`, `README.md`.

---

### 5) Export to ONNX
CLI (quick):
```bash
python -m transformers.onnx --model=facebook/bart-base onnx/
```
Python (custom):
```python
from transformers.onnx import export
from transformers import AutoTokenizer, AutoConfig
from pathlib import Path

model_id = "facebook/bart-base"
config = AutoConfig.from_pretrained(model_id)
tokenizer = AutoTokenizer.from_pretrained(model_id)

export(preprocessor=tokenizer, config=config, opset=14, output=Path("onnx/model.onnx"))
```
References: `src/transformers/onnx/__main__.py`, `src/transformers/onnx/convert.py`.

