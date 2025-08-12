Title: Troubleshooting guide (env, CUDA, tokenizers, memory)

Goal
- Provide a quick triage guide for common issues: environment/version mismatches, CUDA setup, tokenizer install, large model memory, missing optional deps.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- `src/transformers/commands/env.py`
- `src/transformers/utils/import_utils.py`
- `docs/source/en/llm_tutorial.md` (quantization tips)
- `docs/source/en/pipeline_tutorial.md` (device mapping)

Deliverables
- Markdown with a checklist, common errors → fixes, and links to commands like `transformers env`.

Output
- Write to `.devcontext/architecture/onboarding/06-troubleshooting-guide.md`.
