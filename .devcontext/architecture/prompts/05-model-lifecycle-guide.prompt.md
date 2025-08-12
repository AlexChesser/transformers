Title: Model lifecycle guide (load → train → evaluate → save → publish → serve)

Goal
- Document the end-to-end model lifecycle within this repo and the Hub, including artifacts and checkpoints.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `docs/source/en/philosophy.md`
- `src/transformers/__init__.py` (version)
- `src/transformers/utils/hub.py`
- `src/transformers/models/auto/*`
- `docs/source/en/trainer.md`, `docs/source/en/pipeline_tutorial.md`

Deliverables
- One markdown doc with a lifecycle diagram (Mermaid) and brief steps with minimal code.
- List key artifacts: `config.json`, weights, tokenizer files, `generation_config.json`, model card.
- Include a short “publish and consume” section for the Hub.

Output
- Write to `.devcontext/architecture/onboarding/05-model-lifecycle-guide.md`.
