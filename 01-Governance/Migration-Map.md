# Migration Map

**Status:** Migration completed for the current repository material on `main` as of 2026-08-23.

The repository was rebuilt around clear ownership boundaries. Redundant legacy copies were removed only after their useful information had been migrated into the new structure.

| Former location | Final owner | Result |
|---|---|---|
| `02-Orchestration-Primitives/*` core node pages | `03-Canonical-Reference/Nodes/` | Migrated and removed from old location |
| `02-Orchestration-Primitives/Additional-Observed-Nodes.md` | `02-Product-Model/Node-Taxonomy.md` | Split/consolidated; old file removed |
| `03-Evidence/*` node/runtime evidence | `04-Evidence/UI/` and `04-Evidence/Runtime/` | Reclassified and duplicate old files removed |
| `03-Evidence/Agent-Tool-Catalog.md` | `04-Evidence/Tool-Contracts/Agent-Tool-Catalog.md` | Migrated and old copy removed |
| `04-Verification/*` | `05-Verification/Verification-Queue.md` | Consolidated into one active queue; old layer removed |
| `05-Current-Understanding/Current-Understanding-and-Verification-Ledger.md` | `06-Current-Truth/Current-Truth.md` | Consolidated into control tower; old layer removed |
| `README.md` | `README.md` | Rewritten for final architecture |
| `99-Legacy/*` | None | Retired after migration; no longer authoritative |

## Final ownership

```text
Explanation   → 03-Canonical-Reference
Proof         → 04-Evidence
Open question → 05-Verification
Snapshot      → 06-Current-Truth
Test design   → 07-Test-Lab
```

## Cleanup rule

Do not reintroduce parallel copies of canonical explanations or verification queues. When a new runtime test confirms an open question, add the proof to `04-Evidence`, update the canonical reference, remove the question from `05-Verification`, and retain the historical test result.
