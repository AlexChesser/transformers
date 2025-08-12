## Backend support matrix (PyTorch / TensorFlow / Flax)

References: `docs/source/en/index.md`, `docs/source/en/trainer.md`, `docs/source/en/pipeline_tutorial.md`, `docs/source/en/llm_tutorial.md`, `src/transformers/modeling_utils.py`, `src/transformers/modeling_tf_utils.py`, `src/transformers/modeling_flax_utils.py`.

| Feature | PyTorch | TensorFlow | Flax |
|---|---|---|---|
| Training loop | Trainer (first-class) | Keras `.fit()` | Flax training utils; community examples |
| Evaluation | Trainer `.evaluate()` | Keras eval | Flax custom loops |
| Pipelines (inference) | Broad coverage | Broad coverage | Broad coverage (CPU/JAX backends) |
| Text generation (`generate`) | Mature; most features land first | Supported on TF models with generation heads | Supported where implemented; fewer models than PT |
| Mixed precision | AMP/bfloat16 | Keras mixed precision | JAX bfloat16/float16 |
| Compilation | `torch.compile` (2.x) | XLA/`tf.function` | XLA (JAX) |
| Distributed training | Accelerate, FSDP, DeepSpeed | TF strategies | JAX pjit/pmap; community tooling |
| Quantization | bitsandbytes, GPTQ, AWQ, torchao | Limited (backend-specific) | Limited; community projects |
| Device mapping | `accelerate` `device_map="auto"` | N/A | N/A |
| ONNX export | Supported (convert/CLI) | Supported for many models | Limited/indirect |
| Tokenizers (Fast) | Yes | Yes | Yes |
| Vision/audio processors | Yes (`torchvision`, `timm`, `torchaudio`) | Yes (TF ops) | Yes (JAX + libs) |

Notes
- PyTorch typically receives new training/inference features first; TF/Flax support follows where applicable.
- Quantization backends are primarily PyTorch‑focused; verify backend support per project.
- Pipelines cover PT/TF/Flax models; hardware acceleration depends on each backend (CUDA, MPS, XLA).

