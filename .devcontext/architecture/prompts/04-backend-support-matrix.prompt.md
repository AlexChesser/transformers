Title: Backend support matrix (PT/TF/Flax)

Goal
- Create a concise matrix outlining supported features per backend: training, evaluation, generation, pipelines, quantization, export.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `docs/source/en/index.md`
- `docs/source/en/trainer.md`
- `docs/source/en/llm_tutorial.md`
- `docs/source/en/pipeline_tutorial.md`
- Code: `src/transformers/modeling_utils.py`, `src/transformers/modeling_tf_utils.py`, `src/transformers/modeling_flax_utils.py`

Deliverables
- Markdown table with features vs PT/TF/Flax; add brief notes per cell where relevant (e.g., “more mature in PT”).

Output
- Write to `.devcontext/architecture/onboarding/04-backend-support-matrix.md` (include the numeric prefix to ensure natural sorting).
