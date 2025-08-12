Title: Architecture Decision Records (ADRs)

Goal
- Capture concise ADRs for major design choices in this repo (lazy imports, generated pipeline typings, GenerationConfig separation, multi-backend single-tree design).

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `src/transformers/__init__.py` (lazy import structure)
- `src/transformers/pipelines/__init__.py` (generated typing block)
- `src/transformers/generation/configuration_utils.py` (GenerationConfig)
- `src/transformers/utils/import_utils.py` (optional deps)

Deliverables
- Create one ADR per decision with: Context, Decision, Consequences, Alternatives.

Output
- Write ADRs to `.devcontext/architecture/adrs/*.md` (one file per ADR).
