## Security, licensing, and model governance quickref

References: `README.md` (model cards, citation), `src/transformers/modelcard.py` (card generation helpers), Hub guidelines (consult latest on policies and permitted uses).

### Key concepts
- Licenses apply separately to: code, model weights, and datasets.
- Model cards document intended use, limitations, evaluation, and provenance.
- Provenance covers where weights/data came from, processing steps, and who modified them.

### Publishing checklist (models)
- Licensing
  - Choose a clear license for weights (OSS‑compatible or custom). Ensure it’s compatible with any upstream base model.
  - Include third‑party license notices where applicable.
- Artifacts
  - Include `config.json`, weights (prefer `safetensors`), tokenizer/processor files, and (for generative models) `generation_config.json`.
  - Provide a comprehensive model card (`README.md` in repo) with:
    - Intended use and out‑of‑scope use
    - Training data sources and preprocessing summary
    - Evaluation methods/metrics and known limitations/biases
    - Safety considerations and potential risks
    - Version, commit, and library `__version__` used to produce weights
- Provenance and attribution
  - Credit upstream authors and datasets; cite papers (see `README.md` citation format) and include BibTeX if relevant.
  - Document training recipe at a high level (hyperparams, steps, compute env). 
- Compliance
  - Confirm you have rights to redistribute weights and datasets.
  - Consider export controls and restricted content policies; mark gated content if required by the Hub.

### Publishing checklist (datasets)
- Licensing
  - Include dataset license (SPDX identifier if possible) and any usage restrictions.
  - Verify compatibility with downstream distribution (no terms breach).
- Documentation
  - Describe collection process, fields, splits, and known issues/biases.
  - Flag potential PII and sensitive content; describe redaction/mitigation steps.

### Consuming checklist
- Verify licenses
  - Confirm model/dataset licenses allow your intended use (commercial, redistribution, fine‑tuning).
  - Track transitive obligations (attribution, share‑alike, non‑commercial).
- Security posture
  - Prefer `safetensors` over pickle‑based weights.
  - Pin versions and hashes; record Hub commit IDs for reproducibility.
- Responsible use
  - Read model card limitations and evaluation coverage; add safety filters as needed for your application domain.
  - For regulated domains, run domain‑specific validation and maintain an approval trail.

### Model card essentials (minimum viable contents)
- Overview: model family, size, modalities, tasks.
- Intended use: supported and out‑of‑scope use cases.
- Training data: sources, curation, and preprocessing summary.
- Evaluation: datasets, metrics, limitations.
- Risks/safety: potential harms, bias sources, mitigations.
- How to use: minimal code snippet, hardware requirements.
- Versioning: base model tag, your commit hash, `transformers` version.

### Provenance and auditability
- Track and publish: base checkpoint(s), data snapshots, training/eval code versions, config diffs.
- Keep a changelog of fine‑tuning runs and hyperparams.
- Store eval artifacts (metrics JSON, confusion matrices where applicable).

### Practical references in this repo
- Model card utilities: `src/transformers/modelcard.py`
- Citation format example: `README.md` (Citation section)

### Notes
- When in doubt, link to upstream model/dataset cards and clearly describe your deltas.
- Align internal governance (security reviews, legal) with the above checklists before publishing.

