## Troubleshooting guide (env, CUDA, tokenizers, memory)

References: `src/transformers/commands/env.py`, `src/transformers/utils/import_utils.py`, `docs/source/en/llm_tutorial.md`, `docs/source/en/pipeline_tutorial.md`.

### Quick checklist
- Update pip and tools: `pip install -U pip setuptools wheel`
- Print environment: `transformers env | cat` (or `python -m transformers.commands.env | cat`)
- Confirm backend and versions (PT/TF/Flax) match install instructions
- Start small (CPU or a tiny model) to isolate environment issues

### Environment report
Run:
```bash
transformers env | cat
```
Look for: Python, OS, CUDA, GPU, PT/TF versions, optional deps. Attach this to bug reports.

### CUDA/NVIDIA (Linux) issues
- Driver/toolkit mismatch: ensure `nvidia-smi` shows a driver compatible with your CUDA runtime
- Check PyTorch CUDA availability:
```python
import torch; print(torch.cuda.is_available(), torch.version.cuda)
```
- Install the correct PT wheel for your CUDA: `pip install --index-url https://download.pytorch.org/whl/cu121 torch torchvision`
- Container runs: add `--gpus all` and install NVIDIA Container Toolkit
- If out of memory: reduce batch size, use `torch.bfloat16` or fp16, or quantize (see memory section)

### Apple Silicon (macOS) notes
- No CUDA; use `device="mps"` (PyTorch MPS)
- Many CUDA‑only tools (e.g., bitsandbytes CUDA) won’t work; use CPU or Apple‑native stacks where possible
- Some models/ops may fall back to CPU if unsupported on MPS

### Tokenizers and preprocessors
- Fast tokenizers require the `tokenizers` wheel; ensure a compatible Python/OS/arch
- Some models require `sentencepiece`: `pip install sentencepiece`
- Vision/audio pipelines need extras: `Pillow`, `torchvision`, `librosa`, `torchaudio`, `av`
- See optional deps matrix: `.devcontext/architecture/onboarding/03-optional-dependencies-matrix.md`

### Large model memory / OOM
- Enable model sharding: `from_pretrained(..., device_map="auto")` (requires `accelerate`)
- Quantize (PT): `bitsandbytes` 8/4‑bit; consider GPTQ/AWQ where relevant
- Use smaller `max_new_tokens`, smaller batch size, gradient checkpointing for training
- Offload to CPU/disk (Accelerate config) when GPU RAM is insufficient

### Pipelines and devices
- CPU default; set `device=0` for CUDA, `device="mps"` for Apple GPUs
- For large models, prefer `device_map="auto"` over manual device
- Batch inputs by passing a list; measure performance impact per hardware

### Common errors → fixes
- “CUDA not available” → install CUDA‑enabled PT wheel; verify `nvidia-smi` and drivers
- “No module named tokenizers/sentencepiece” → `pip install tokenizers sentencepiece`
- “Out of memory” → reduce batch, quantize, `device_map="auto"`, offload
- “Operator not supported on MPS” → run on CPU or CUDA; try nightly PT if recently added
- “onnxruntime too old” → upgrade: `pip install -U onnx onnxruntime`

### Optional dependencies
Transformers gates many features behind optional deps. See availability checks in:
`src/transformers/utils/import_utils.py`

### When opening an issue
- Include `transformers env` output
- Exact install commands and versions
- Minimal repro (model id, code, full traceback)

