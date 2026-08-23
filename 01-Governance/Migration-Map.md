# Migration Map

The repository is being rebuilt rather than edited in place. Existing content remains temporarily in the old structure while each piece is assigned a new owner.

| Existing location | New owner | Action |
|---|---|---|
| `02-Orchestration-Primitives/Start.md` | `03-Canonical-Reference/Nodes/Start.md` | Migrate and slim |
| `02-Orchestration-Primitives/Human-Input.md` | `03-Canonical-Reference/Nodes/Human-Input.md` | Migrate and slim |
| `02-Orchestration-Primitives/Approval.md` | `03-Canonical-Reference/Nodes/Approval.md` | Migrate and slim |
| `02-Orchestration-Primitives/Rule.md` | `03-Canonical-Reference/Nodes/Rule.md` | Migrate and slim |
| `02-Orchestration-Primitives/Decision-Tree.md` | `03-Canonical-Reference/Nodes/Decision-Tree.md` | Migrate, correct wording, slim |
| `02-Orchestration-Primitives/Script.md` | `03-Canonical-Reference/Nodes/Script.md` | Migrate and slim |
| `02-Orchestration-Primitives/Variable-Update.md` | `03-Canonical-Reference/Nodes/Variable-Update.md` | Migrate and slim |
| `02-Orchestration-Primitives/Agent.md` | `03-Canonical-Reference/Nodes/Agent.md` | Migrate and separate evidence |
| `02-Orchestration-Primitives/Additional-Observed-Nodes.md` | `02-Product-Model/Node-Taxonomy.md` + future canonical pages | Split by capability |
| `03-Evidence/*` node evidence | `04-Evidence/UI/` or `04-Evidence/Runtime/` | Split by evidence type |
| `03-Evidence/Agent-Tool-Catalog.md` | `04-Evidence/Tool-Contracts/` + `02-Product-Model/Tool-and-Integration-Model.md` | Split contract vs conceptual model |
| `04-Verification/Verification-Queue.md` | `05-Verification/Verification-Queue.md` | Rebuild as compact queue |
| `04-Verification/*` | `05-Verification/Nodes/` or `05-Verification/Tools/` | Reclassify and slim |
| `05-Current-Understanding/Current-Understanding-and-Verification-Ledger.md` | `06-Current-Truth/Current-Truth.md` | Compress to control tower |
| `README.md` | `README.md` | Replace with architecture/navigation |

## Migration policy

No old file is deleted until its unique information has been migrated and reviewed.

Repeated information is consolidated into one canonical owner instead of copied into multiple new locations.
