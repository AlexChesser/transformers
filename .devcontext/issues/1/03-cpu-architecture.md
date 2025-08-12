I’m going to scan the repository documentation and docker files to identify which non-NVIDIA GPU backends are supported (e.g., ROCm/AMD, Apple MPS, Intel/XPU/OpenVINO, Habana Gaudi, AWS Neuron, DirectML/CoreML), so we can decide what container variants to plan for.
Search files...
Search files...
Search files...
Search files...
Search files...
Search files...
Ran tool
Ran tool
Ran tool
Ran tool
Ran tool
Ran tool
Ran tool
Ran tool
Ran tool

Ran tool

I scanned the repo’s docs and Dockerfiles. There is first-class support beyond NVIDIA CUDA: AMD ROCm, Intel XPU, Apple MPS, and Intel Habana Gaudi; AWS Neuron is also referenced for inference/training via Optimum.

### What’s supported in this repo (non‑NVIDIA)
- AMD ROCm (AMD GPUs)
  - Dockerfiles exist.
  - Example:
```1:10:docker/transformers-pytorch-amd-gpu/Dockerfile
FROM rocm/pytorch:rocm6.4.1_ubuntu24.04_py3.12_pytorch_release_2.7.1
LABEL maintainer="Hugging Face"
ARG DEBIAN_FRONTEND=noninteractive
```
- Intel XPU (Intel GPUs)
  - Dockerfile exists and installs PyTorch from the XPU wheel index.
```61:67:docker/transformers-pytorch-xpu/Dockerfile
RUN pip install --upgrade pip
RUN pip install triton==3.3.0
RUN pip install torch==2.7.0 torchvision==0.22.0 torchaudio==2.7.0 --index-url https://download.pytorch.org/whl/xpu --no-cache-dir
```
- Apple Silicon MPS (Apple GPUs, host macOS)
  - Documented for training/inference; typically not run via Docker.
```16:25:docs/source/en/perf_train_special.md
# Apple Silicon
Apple Silicon (M series) ... [MPS] ...
The `mps` backend requires macOS 12.3 or later.
[`TrainingArguments`] and [`Trainer`] detects and sets the backend device to `mps` ...
```
- Intel Habana Gaudi (HPU)
  - Dedicated docs and runtime expectations.
```16:25:docs/source/en/perf_train_gaudi.md
# Intel Gaudi
[`TrainingArguments`], [`Trainer`] and [`Pipeline`] detect and set the backend device to `hpu` ...
PT_HPU_LAZY_MODE=0
```
- AWS Neuron (Inferentia/Trainium)
  - TorchScript/Optimum pathway described.
```115:128:docs/source/en/torchscript.md
## Deploy to AWS
... use torch.neuron.trace(model, ...)
import torch.neuron
torch.neuron.trace(model, [tokens_tensor, segments_tensors])
```

### Practical container planning
If you’re building images, plan at least these variants:
- CPU_ONLY
- GPU_CUDA (NVIDIA)
- GPU_ROCM (AMD)
- GPU_XPU (Intel)
- ACCEL_HPU (Habana Gaudi)
- Optional: ACCEL_NEURON (AWS Neuron; usually via AWS-provided bases)
- Note: Apple MPS is host-native on macOS; don’t containerize it.

### Runtime notes
- ROCm: expose `/dev/kfd` and `/dev/dri`, set `ROCR_VISIBLE_DEVICES`.
- XPU: set `ZE_AFFINITY_MASK` to choose devices.
- HPU: use the Habana runtime (`--runtime=habana`), `HABANA_VISIBLE_DEVICES`, set `PT_HPU_LAZY_MODE=0` if needed.
- MPS: macOS 12.3+, `PYTORCH_ENABLE_MPS_FALLBACK=1` for CPU fallback.
- Bitsandbytes/quantization: mostly CUDA; multi-backend is emerging but not yet universal here.

- For CUDA reference:
```1:8:docker/transformers-all-latest-gpu/Dockerfile
FROM nvidia/cuda:12.6.0-cudnn-devel-ubuntu22.04
LABEL maintainer="Hugging Face"
```

Summary
- Yes—non‑NVIDIA accelerators are supported: AMD ROCm, Intel XPU, Apple MPS, Habana Gaudi, and AWS Neuron.
- For Docker, plan separate images: `CPU_ONLY`, `GPU_CUDA`, `GPU_ROCM`, `GPU_XPU`, and optionally `ACCEL_HPU` and `ACCEL_NEURON`.
- Apple MPS is host-native (macOS), not a container target.