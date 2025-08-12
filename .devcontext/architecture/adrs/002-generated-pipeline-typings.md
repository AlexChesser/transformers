## ADR 002: Generated pipeline typings

### Context
`pipelines/__init__.py` exposes many task-specific `pipeline(...)` overloads. Keeping these typings accurate by hand is error-prone.

References: `src/transformers/pipelines/__init__.py` (auto-generated typing section and regeneration instructions).

### Decision
- Auto-generate the overload block from the source registry using a script (`utils/check_pipeline_typing.py --fix_and_overwrite`).

### Consequences
- Accurate type hints synchronized with the registry; better IDE support.
- Contributors must avoid manual edits to the generated block and run the script after relevant changes.

### Alternatives
- Manual maintenance of overloads (high drift risk).
- Single untyped signature (worse developer experience).

