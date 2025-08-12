## ADR 004: Multi‑backend single code tree (PT/TF/Flax)

### Context
Transformers supports PyTorch, TensorFlow, and Flax/JAX from one repository. Splitting per-backend would fragment APIs and documentation.

References: `src/transformers/modeling_utils.py`, `src/transformers/modeling_tf_utils.py`, `src/transformers/modeling_flax_utils.py`, `src/transformers/utils/import_utils.py` (optional deps).

### Decision
- Keep a unified codebase with conditional imports and optional dependency checks.
- Expose common abstractions (PretrainedConfig, Auto classes, tokenizers/processors) across backends.

### Consequences
- Higher code complexity and conditional paths; greater test surface.
- Unified UX and documentation; easier cross-backend parity.

### Alternatives
- Split repositories per backend: simpler internals but worse user experience and duplication.

