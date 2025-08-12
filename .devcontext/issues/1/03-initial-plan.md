### Devcontainer plan for `transformers` (MVP → multi‑accelerator)

**Executive summary**: Introduce a modular Dev Container that standardizes local development, with a safe CPU‑only default and opt‑in overrides for CUDA and other accelerators. Start with a minimal MVP (CPU + CUDA for WSL/Linux) and evolve toward additional backends (ROCm, XPU), documentation, and light validation tooling. All changes are additive, isolated under `.devcontainer/`, and do not affect CI or runtime by default.

---

### Table of contents (phases, tracks, PRs)

- **Phase 1: MVP (safe, minimal, useful)**
  - [PR‑1](#pr-1-add-devcontainer-skeleton-cpu_only-default): Add Dev Container skeleton, CPU_ONLY default
  - [PR‑2](#pr-2-add-cuda-override-and-gpu-smoke-check): Add CUDA override and GPU smoke check

- **Phase 2: Host/arch coverage**
  - [PR‑3](#pr-3-add-apple-silicon-arm64-cpu-only-override): Add Apple Silicon ARM64 CPU‑only override

- **Phase 3: Additional accelerators (opt‑in)**
  - [PR‑4](#pr-4-add-amd-rocm-override): Add AMD ROCm override
  - [PR‑5](#pr-5-add-intel-xpu-override): Add Intel XPU override
  - [PR‑6-optional](#pr-6-optional-outline-for-habana-gaudi-hpu-and-aws-neuron): Outline for Habana Gaudi (HPU) and AWS Neuron

- **Phase 4: Docs and quality**
  - [PR‑7](#pr-7-developer-docs-and-usage-recipes): Developer docs and usage recipes
  - [PR‑8](#pr-8-local-automation-make-targets-and-lean-smoke-scripts): Local automation (Make targets) and lean smoke scripts

---

### Design principles and contracts

- **Modular by override**: Keep `CPU_ONLY` as the default in `.devcontainer/devcontainer.json`. Provide opt‑in overrides users can copy to `devcontainer.local.json` for their accelerator.
- **Reuse existing Dockerfiles**: When practical, point Dev Container builds at existing files in `docker/*` (e.g., `docker/transformers-pytorch-gpu/Dockerfile`). Prefer reuse over duplicating base images.
- **Layered installs**: Follow onboarding guidance to layer base, dev, and optional extras. Prefer editable installs (`pip install -e .`) with the repo mount.
- **Isolation and safety**: All files live under `.devcontainer/` and optional `scripts/devcontainer/`. No changes to package code or CI by default.
- **Backends scope**: MVP targets CPU and CUDA on Linux/WSL. Apple Silicon uses CPU‑only container; GPU work happens host‑side via MPS.

Devcontainer config contract (API):
- `/.devcontainer/devcontainer.json` — base CPU_ONLY config (always present).
- `/.devcontainer/devcontainer.local.json` — user override (ignored by Git). Document supported keys:
  - `build.dockerfile`, `build.context`, `build.args`
  - `runArgs` (e.g., `--gpus all`, device mounts)
  - `remoteUser`, `containerUser`
  - `features` (optional), `postCreateCommand`
- Provided example overrides (users copy the one they need into `devcontainer.local.json`):
  - `/.devcontainer/overrides/devcontainer.GPU_CUDA.json`
  - `/.devcontainer/overrides/devcontainer.APPLE_ARM64.json`
  - `/.devcontainer/overrides/devcontainer.GPU_ROCM.json`
  - `/.devcontainer/overrides/devcontainer.GPU_XPU.json`
- Optional Dockerfiles if we cannot directly reuse existing ones:
  - `/.devcontainer/dockerfiles/cpu.Dockerfile`
  - `/.devcontainer/dockerfiles/arm64.Dockerfile`

Runtime environment contract:
- Common env: `HF_HOME`, `TRANSFORMERS_CACHE`, `PYTHONUNBUFFERED=1`.
- CUDA: `--gpus all` and NVIDIA Container Toolkit required.
- ROCm: expose `/dev/kfd` and `/dev/dri`; env `ROCR_VISIBLE_DEVICES` optional.
- XPU: expose `/dev/dri`; env `ZE_AFFINITY_MASK` optional.
- Apple Silicon: CPU‑only inside container. For MPS, run Python on host.

Smoke tooling contract:
- `scripts/devcontainer/smoke_env.py` — prints `transformers env` and exits non‑zero on obvious misconfig.
- `scripts/devcontainer/smoke_torch_cuda.py` — imports torch, checks `torch.cuda.is_available()`, prints device info.

Function signatures:
```python
def verify_transformers_environment(strict: bool = False) -> None
def verify_torch_cuda(min_major: int | None = None) -> None
```

---

### PR‑by‑PR plan

#### PR‑1: Add Devcontainer skeleton (CPU_ONLY default)
Summary: Introduce a safe default Dev Container for all contributors that works everywhere (Linux, WSL, macOS via Rosetta/ARM64 emulation when needed). No GPU assumptions.

Files added:
- `.devcontainer/devcontainer.json`
- `.devcontainer/.gitignore` (ignore `devcontainer.local.json`)
- `.devcontainer/dockerfiles/cpu.Dockerfile` (only if not directly using an existing Dockerfile)
- `.devcontainer/scripts/postCreate.sh` (installs editable deps, optional `pre-commit` hook)
- `scripts/devcontainer/smoke_env.py`

devcontainer.json essentials:
```json
{
  "name": "transformers-cpu",
  "build": { "dockerfile": ".devcontainer/dockerfiles/cpu.Dockerfile", "context": ".." },
  "remoteUser": "vscode",
  "postCreateCommand": "bash .devcontainer/scripts/postCreate.sh",
  "updateContentCommand": "python -m transformers.commands.env | cat",
  "features": {},
  "containerEnv": {
    "HF_HOME": "/workspace/.cache/huggingface",
    "TRANSFORMERS_CACHE": "/workspace/.cache/transformers",
    "PYTHONUNBUFFERED": "1"
  }
}
```

cpu.Dockerfile essentials:
```Dockerfile
FROM ubuntu:22.04
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    python3 python3-venv python3-pip git build-essential \
    && rm -rf /var/lib/apt/lists/*
RUN python3 -m pip install -U pip uv
```

postCreate.sh essentials:
```bash
set -euo pipefail
uv pip install -U "accelerate datasets evaluate pytest pre-commit"
uv pip install -e .[torch]
pre-commit install || true
python -m transformers.commands.env | cat
```

Safety: All files are new under `.devcontainer/` and `scripts/devcontainer/`. No repo build/test changes. Safe to merge and ship.

#### PR‑2: Add CUDA override and GPU smoke check
Summary: Provide an opt‑in CUDA devcontainer override and a simple GPU verification script.

Files added:
- `.devcontainer/overrides/devcontainer.GPU_CUDA.json`
- `scripts/devcontainer/smoke_torch_cuda.py`

devcontainer.GPU_CUDA.json essentials:
```json
{
  "name": "transformers-cuda",
  "build": { "dockerfile": "docker/transformers-pytorch-gpu/Dockerfile", "context": ".." },
  "runArgs": ["--gpus", "all"],
  "remoteUser": "vscode",
  "postCreateCommand": "bash .devcontainer/scripts/postCreate.sh && python scripts/devcontainer/smoke_torch_cuda.py"
}
```

smoke_torch_cuda.py essentials:
```python
import sys, torch
ok = torch.cuda.is_available()
print({"cuda_available": ok, "device_count": torch.cuda.device_count()})
sys.exit(0 if ok else 1)
```

Safety: Purely opt‑in via `devcontainer.local.json`. No behavior change for non‑GPU users.

#### PR‑3: Add Apple Silicon ARM64 CPU‑only override
Summary: Provide an ARM64 build option for Apple Silicon. Container remains CPU‑only; GPU acceleration via MPS is host‑native (documented).

Files added:
- `.devcontainer/overrides/devcontainer.APPLE_ARM64.json`
- `.devcontainer/dockerfiles/arm64.Dockerfile` (if not reusing an existing Dockerfile)

devcontainer.APPLE_ARM64.json essentials:
```json
{
  "name": "transformers-arm64",
  "build": {
    "dockerfile": ".devcontainer/dockerfiles/arm64.Dockerfile",
    "context": "..",
    "args": { "TARGETPLATFORM": "linux/arm64" }
  },
  "remoteUser": "vscode",
  "postCreateCommand": "bash .devcontainer/scripts/postCreate.sh"
}
```

arm64.Dockerfile essentials:
```Dockerfile
FROM ubuntu:22.04
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    python3 python3-venv python3-pip git build-essential \
    && rm -rf /var/lib/apt/lists/*
RUN python3 -m pip install -U pip uv
```

Safety: Opt‑in override. No impact on default or CI.

#### PR‑4: Add AMD ROCm override
Summary: Reuse `docker/transformers-pytorch-amd-gpu/Dockerfile` with required device mappings.

Files added:
- `.devcontainer/overrides/devcontainer.GPU_ROCM.json`

devcontainer.GPU_ROCM.json essentials:
```json
{
  "name": "transformers-rocm",
  "build": { "dockerfile": "docker/transformers-pytorch-amd-gpu/Dockerfile", "context": ".." },
  "runArgs": ["--device=/dev/kfd", "--device=/dev/dri", "--group-add=video"],
  "remoteUser": "vscode",
  "postCreateCommand": "bash .devcontainer/scripts/postCreate.sh"
}
```

Safety: Opt‑in override, device access delegated to the user.

#### PR‑5: Add Intel XPU override
Summary: Reuse `docker/transformers-pytorch-xpu/Dockerfile` and expose `/dev/dri`.

Files added:
- `.devcontainer/overrides/devcontainer.GPU_XPU.json`

devcontainer.GPU_XPU.json essentials:
```json
{
  "name": "transformers-xpu",
  "build": { "dockerfile": "docker/transformers-pytorch-xpu/Dockerfile", "context": ".." },
  "runArgs": ["--device=/dev/dri"],
  "remoteUser": "vscode",
  "containerEnv": { "ZE_AFFINITY_MASK": "0" },
  "postCreateCommand": "bash .devcontainer/scripts/postCreate.sh"
}
```

Safety: Opt‑in override. No baseline changes.

#### PR‑6 (optional): Outline for Habana Gaudi (HPU) and AWS Neuron
Summary: Provide documentation stubs and placeholders for future accelerators that typically require vendor base images or specific runtimes.

Files added:
- `docs/source/en/devcontainer_accelerators.md` (section for HPU/Neuron with links)
- `.devcontainer/overrides/devcontainer.ACCEL_HPU.json` (commented example referencing vendor images)
- `.devcontainer/overrides/devcontainer.ACCEL_NEURON.json` (commented example referencing AWS Neuron images)

Safety: Docs and commented examples only.

#### PR‑7: Developer docs and usage recipes
Summary: Document how to use the Dev Container, override workflow, and known limitations (e.g., Apple MPS outside container).

Files added:
- `.devcontainer/README.md`
- `docs/source/en/devcontainer.md` (short page; links from `docker/README.md`)

Contents essentials:
- How to open in VS Code / Cursor
- How to select an override (copy chosen file to `devcontainer.local.json`)
- How to run smoke checks (`transformers env`, torch CUDA check)
- Caching strategy (`HF_HOME`, `TRANSFORMERS_CACHE`)

Safety: Docs only.

#### PR‑8: Local automation (Make targets) and lean smoke scripts
Summary: Add small, local convenience targets and keep validation outside CI by default.

Files added:
- `Makefile` additions (non‑breaking): `devcontainer-build`, `devcontainer-reopen`, `dev-smoke`
- `scripts/devcontainer/smoke_env.py` (already added in PR‑1)

Make targets (illustrative):
```Makefile
devcontainer-build:
	@echo "Building devcontainer (base or local override)"

dev-smoke:
	python -m transformers.commands.env | cat
	python scripts/devcontainer/smoke_env.py
```

Safety: Local tooling; no enforced CI.

---

### Why each PR is safe to ship
- **PR‑1**: Adds isolated devcontainer assets and a post‑create script. No changes to application code or CI. Reversible.
- **PR‑2**: Adds an opt‑in override and a tiny smoke script. No default runtime changes.
- **PR‑3**: Adds an ARM64 override and optional Dockerfile. Opt‑in only.
- **PR‑4/5**: Add overrides referencing existing Dockerfiles. Opt‑in only; device access explicitly required.
- **PR‑6**: Documentation and commented examples only.
- **PR‑7/8**: Documentation and local convenience targets. No CI or runtime impact.

---

### Parallelizable tracks
- MVP (PR‑1, PR‑2) is sequential (base → CUDA). Once MVP lands:
  - Apple ARM64 (PR‑3) can proceed in parallel with ROCm (PR‑4) and XPU (PR‑5).
  - Docs (PR‑7) and Make targets (PR‑8) can proceed in parallel after PR‑1.

---

### Acceptance criteria per phase
- **Phase 1**: Open repo in devcontainer (CPU_ONLY) and run `transformers env`. For CUDA override, `torch.cuda.is_available()` is true and device count ≥ 1.
- **Phase 2**: Apple Silicon users can open the container, run `transformers env`, and execute CPU inference/training samples.
- **Phase 3**: ROCm and XPU users can open the CUDA‑analogous overrides and verify device visibility using backend‑specific instructions.
- **Phase 4**: Docs present, easy workflow for overrides, and basic local Make targets.

---

### Notes and references
- Reuse of `docker/*` images minimizes drift with existing CI containers. See `docker/README.md` for guidance on large vs light images.
- Onboarding guidance: `.devcontext/architecture/onboarding/01-developer-setup-and-workflow.md`.
- Feature request and MVP scope: `.devcontext/issues/1/01-issue.txt`.


