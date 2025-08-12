# Architecture diagrams (Mermaid)

This folder contains concise Mermaid diagrams for the Transformers project:

- `c4-l1-context.mmd`: C4 Level 1 (Context) — high-level ecosystem view
- `c4-l2-containers.mmd`: C4 Level 2 (Containers) — internal containers and neighbors
- `sequence-pipeline-inference.mmd`: Sequence — pipeline inference
- `sequence-trainer-training-loop.mmd`: Sequence — Trainer training loop
- `sequence-hub-from_pretrained-and-push_to_hub.mmd`: Sequence — Hub interactions

## Edit
- Open any `.mmd` in the Mermaid Live Editor: https://mermaid.live
- Keep diagrams small and focused.
- Source references:
  - Ecosystem: `docs/source/en/index.md`
  - Internals: `src/transformers/*` (pipelines, Trainer, models, utils)
  - Sequences: `docs/source/en/pipeline_tutorial.md`, `docs/source/en/trainer.md`, `docs/source/en/llm_tutorial.md`

## Export (SVG/PNG/PDF)
Using Mermaid CLI:

```bash
# macOS (Homebrew)
brew install mermaid-cli

# Export examples
mmdc -i c4-l1-context.mmd -o c4-l1-context.svg
mmdc -i c4-l2-containers.mmd -o c4-l2-containers.svg
mmdc -i sequence-pipeline-inference.mmd -o sequence-pipeline-inference.svg
mmdc -i sequence-trainer-training-loop.mmd -o sequence-trainer-training-loop.svg
mmdc -i sequence-hub-from_pretrained-and-push_to_hub.mmd -o sequence-hub-from_pretrained-and-push_to_hub.svg
```

To export all diagrams in one go:

```bash
for f in *.mmd; do mmdc -i "$f" -o "${f%.mmd}.svg"; done
```

## Notes
- These diagrams are intentionally simplified to aid onboarding.
- For deeper details (e.g., per-model internals), consult code in `src/transformers/models/*` and generation/trainer modules.