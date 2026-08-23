# Additional Node Evidence

**Evidence date:** 2026-08-23  
**Status:** Capability-level evidence boundary; runtime verification is intentionally incomplete for these additional node families.

This file exists only for node families that do not yet have dedicated configuration evidence comparable to the core primitive pages.

| Capability | Current status | Canonical location |
|---|---|---|
| LLM | Observed at capability level; detailed UI/runtime evidence pending | `02-Orchestration-Primitives/Additional-Observed-Nodes.md` |
| External Agent | Observed at capability level; detailed UI/runtime evidence pending | same |
| Compute | Observed at capability level; detailed UI/runtime evidence pending | same |
| Subflow | Observed at capability level; detailed UI/runtime evidence pending | same |
| Handoff | Observed at capability level; detailed UI/runtime evidence pending | same |
| Guardrail | Observed at capability level; detailed UI/runtime evidence pending | same |
| Output | Capability observed; expression/runtime contract still being mapped | same |

**Human Input is not listed here anymore.** It has dedicated evidence and runtime-confirmed end-to-end tests.

## Evidence boundary

For these additional capabilities, do not infer exact configuration fields, output schemas, routing semantics, persistence behavior, or error handling from general concepts alone.

When a capability is next investigated, capture:

1. configuration UI
2. visible inputs and outputs
3. advanced settings
4. state updates
5. output variables
6. routing handles
7. runtime execution
8. downstream consumption
9. failure behavior
10. user-visible result where applicable

Only then promote the capability into a dedicated canonical node/evidence record.
