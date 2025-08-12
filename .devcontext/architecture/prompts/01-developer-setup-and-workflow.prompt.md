Title: Developer setup and workflow guide

Goal
- Produce a concise onboarding document for new contributors covering local setup, environment, extras per backend, running tests/examples, and common dev workflows in this repository.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Scope
- Installation paths for PyTorch, TensorFlow, and Flax variants; extras (e.g., accelerate, bitsandbytes, tokenizers).
- Local dev loop: editable install, running unit tests, style/lint guidance, useful scripts.
- Running key examples (pipelines, Trainer), and verifying environment with CLI.
- Where to find docs and examples in-repo.

Reference files to cite
- `README.md`
- `docs/source/en/index.md`
- `docs/source/en/pipeline_tutorial.md`
- `docs/source/en/trainer.md`
- `docs/source/en/llm_tutorial.md`
- `src/transformers/commands/env.py`
- `src/transformers/utils/import_utils.py`

Deliverables
- A markdown doc optimized for new developers. Include:
  - OS/prereq checklist and Python version targets.
  - Install commands for common paths: `pip install "transformers[torch]"`, TF/Flax variants, optional extras.
  - How to run: `transformers env`, simple pipeline, simple Trainer run.
  - How to run unit tests selectively; how to run example scripts.
  - Links to docs and examples inside the repo.

Requirements
- Cite relevant code or docs using file paths in backticks (e.g., `docs/source/en/trainer.md`).
- Keep it self-contained and runnable without internet beyond pip installs.
- Include a short troubleshooting section linking to the troubleshooting prompt deliverable.

ADDITIONAL Requirements
- assume that this workflow will be used an an input to design the dockerfile and devcontainer.


Output
- Write to `.devcontext/architecture/onboarding/01-developer-setup-and-workflow.md` (include the numeric prefix to ensure natural sorting).
