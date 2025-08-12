## ADR 001: Lazy imports and namespace initialization

### Context
The library must expose a very large public API while minimizing import time and avoiding hard dependencies on optional backends and integrations.

References: `src/transformers/__init__.py` (lazy module and `_import_structure`), `src/transformers/utils/import_utils.py` (availability checks).

### Decision
- Use a lazy import mechanism that defers importing submodules until names are actually accessed.
- Maintain a centralized `_import_structure` and `TYPE_CHECKING` branch to present names to static tools without importing heavy modules at runtime.

### Consequences
- Faster `import transformers` with reduced baseline memory/IO.
- Optional dependencies can be probed and errors surfaced only when features are used.
- Increased complexity in init logic; contributors must update `_import_structure` and typing branches consistently.

### Alternatives
- Eager imports: simpler but slower, forces many optional dependencies to be installed.
- Multiple smaller packages per backend: fragmentation of the ecosystem and worse UX.

