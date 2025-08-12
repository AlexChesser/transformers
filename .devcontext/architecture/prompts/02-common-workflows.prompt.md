Title: Common workflows playbook (task-centric)

Goal
- Create short, copy-pastable guides for key workflows:
  1) Run inference with pipelines
  2) Train with Trainer
  3) Add a new model (high-level pointers)
  4) Publish to Hub
  5) Export to ONNX

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `README.md`
- `docs/source/en/pipeline_tutorial.md`
- `docs/source/en/trainer.md`
- `docs/source/en/llm_tutorial.md`
- `src/transformers/pipelines/__init__.py`
- `src/transformers/models/auto/*`
- `src/transformers/onnx/*`
- `src/transformers/utils/hub.py`

Deliverables
- A markdown doc with 5 sections, each 10–20 lines, minimal code blocks.
- Include install prerequisites and minimal runnable snippets.
- Point to key code and docs for deeper dives.

Output
- Write to `.devcontext/architecture/onboarding/02-common-workflows.md` (include the numeric prefix to ensure natural sorting).
