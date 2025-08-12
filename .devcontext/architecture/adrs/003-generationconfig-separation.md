## ADR 003: Separate GenerationConfig from model config

### Context
Decoding parameters were historically mixed into model configs. This couples generation behavior to architecture settings and complicates reuse.

References: `src/transformers/generation/configuration_utils.py`, deprecation notice in `configuration_utils.py` about setting generation params in model config.

### Decision
- Store decoding parameters in `GenerationConfig` (separate JSON file, loadable via `generation_config.json`).
- Models expose `model.generation_config`; users can override per-call with `generate(..., **kwargs)` or by passing a `GenerationConfig`.

### Consequences
- Clear separation of concerns; easier to ship multiple decoding presets.
- Backward compatibility layer required to read legacy fields.

### Alternatives
- Keep generation params in model config: simpler but conflates concerns and hinders portability.

