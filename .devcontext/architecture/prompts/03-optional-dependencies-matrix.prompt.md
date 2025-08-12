Title: Optional dependencies and extras matrix

Goal
- Produce a compact table mapping feature areas to extras/dependencies, minimal versions, and install hints.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `src/transformers/utils/import_utils.py`
- `docs/source/en/index.md`
- `docs/source/en/llm_tutorial.md`
- `docs/source/en/trainer.md`
- `docs/source/en/pipeline_tutorial.md`

Deliverables
- One markdown page with a table:
  - Columns: Bounded context | Feature | Required | Optional | Install hints | Notes
  - Keep rows actionable and aligned with `import_utils.py` availability checks.

Output
- Write to `.devcontext/architecture/onboarding/03-optional-dependencies-matrix.md` (include the numeric prefix to ensure natural sorting).
