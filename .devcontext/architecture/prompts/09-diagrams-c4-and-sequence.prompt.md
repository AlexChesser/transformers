Title: C4 and sequence diagrams for onboarding

Goal
- Generate C4 L1 (Context) and L2 (Container) diagrams, and sequence diagrams for top workflows.

Linked files available
- @02-architectural-research.md
- @03-features-overview.md

Reference files to cite
- Context: `docs/source/en/index.md` (ecosystem positioning)
- Containers: `src/transformers/*` (pipelines, Trainer, models, utils)
- Sequences: `docs/source/en/pipeline_tutorial.md`, `docs/source/en/trainer.md`, `docs/source/en/llm_tutorial.md`

Deliverables
- Mermaid sources for:
  - C4 L1: Transformers ↔ Hub ↔ Datasets ↔ Accelerate/PEFT ↔ Export/Inference engines.
  - C4 L2: API/CLI/Backends/Optional deps containers.
  - Sequence: Pipeline inference; Trainer training loop; Hub `from_pretrained` and `push_to_hub`.
- Keep each diagram focused and small.

Output
- Write diagrams to `.devcontext/architecture/diagrams/*.mmd` with a brief README at `.devcontext/architecture/diagrams/README.md` explaining how to edit/regenerate.
