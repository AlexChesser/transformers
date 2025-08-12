## Optional dependencies and extras matrix

References: `src/transformers/utils/import_utils.py`, `docs/source/en/index.md`, `docs/source/en/pipeline_tutorial.md`, `docs/source/en/trainer.md`, `docs/source/en/llm_tutorial.md`.

| Bounded context | Feature | Required | Optional | Install hints | Notes |
|---|---|---|---|---|---|
| Model Lifecycle | Load/Save/Hub (`from_pretrained`/`save_pretrained`/`push_to_hub`) | transformers, backend (torch/tf/flax), huggingface_hub, safetensors | tokenizers, datasets | pip install "transformers[torch]" | Works with any backend; caches artifacts locally |
| Model Lifecycle | Auto registries (AutoConfig/AutoModel*/AutoTokenizer) | transformers | sentencepiece (some models) | pip install sentencepiece | Needed for SP-based tokenizers |
| Training & Evaluation | Trainer (PyTorch) | transformers, torch, accelerate (>=0.26) | datasets, evaluate, deepspeed, peft, bitsandbytes | pip install "transformers[torch] accelerate datasets evaluate" | Deepspeed for large scale; PEFT for LoRA/PEFT |
| Training & Evaluation | TF/Flax training | transformers, tensorflow or flax | datasets | pip install "transformers[tf]" or "transformers[flax]" | Use native Keras fit/Flax loops |
| Inference & Serving | Pipelines | transformers, backend | accelerate (device_map="auto"), tokenizers | pip install "transformers[torch] accelerate" | device_map enables big-model sharding |
| Inference & Serving | Text generation (`generate`) | transformers, backend | bitsandbytes, accelerate, sentencepiece | pip install bitsandbytes | Bitsandbytes for memory‑efficient inference |
| Inference & Serving | Vision pipelines | transformers, backend | Pillow, torchvision, timm | pip install Pillow torchvision timm | Some models require timm |
| Inference & Serving | Audio pipelines | transformers, backend | librosa, torchaudio | pip install librosa torchaudio | Backend‑specific ops may be used |
| Inference & Serving | Video pipelines | transformers, backend | av | pip install av | For video decoding |
| Data Prep & Pre/Post | Tokenizers (Fast/slow) | tokenizers (fast) | sentencepiece | pip install tokenizers sentencepiece | Fast is Rust backend |
| Data Prep & Pre/Post | Processors (image/audio/multimodal) | transformers | Pillow, librosa, torchvision | pip install Pillow librosa torchvision | Modality‑specific |
| Optimization & Packaging | Quantization | transformers | bitsandbytes, auto_gptq, awq, torchao, autoround | pip install bitsandbytes | Choose backend appropriate to hardware |
| Optimization & Packaging | Device mapping | transformers, accelerate (>=0.26) | — | pip install accelerate | Automatically shards across devices |
| Optimization & Packaging | ONNX export | transformers | onnx, onnxruntime (>=1.13) | pip install onnx onnxruntime | Export models for alt runtimes |
| Observability & Diagnostics | Env report CLI | transformers | coloredlogs | pip install coloredlogs | `transformers env` prints versions/devices |

