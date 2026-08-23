# Documentation Architecture

The repository uses one owner per knowledge class.

```text
01 Governance
   ↓ defines how knowledge is handled
02 Product Model
   ↓ defines the conceptual map of QI Studio
03 Canonical Reference
   ↓ explains established behavior and design guidance
04 Evidence
   ↓ records what was actually observed or tested
05 Verification
   ↓ records unresolved questions and planned tests
06 Current Truth
   ↓ compact current state and active truth boundary
07 Test Lab
   ↓ reusable test plans, fixtures, and test records
99 Legacy
   ↓ temporary holding area during migration only
```

## Layer ownership

### `01-Governance/`
Rules about evidence, documentation, naming, maintenance, and interpretation.

### `02-Product-Model/`
The system map: node families, variable/state model, agent capability model, tool taxonomy, and other high-level concepts. This layer should not claim detailed runtime behavior unless established elsewhere.

### `03-Canonical-Reference/`
The maintained explanation of how a capability currently works. One canonical page per node or capability family. It should be concise, internally consistent, and linked to supporting evidence.

### `04-Evidence/`
Primary evidence records, runtime tests, UI captures, tool contracts, and historical regressions. Evidence describes what happened, not what we wish the product did.

### `05-Verification/`
Active unanswered questions only. Each item should have a clear test objective and a link to its evidence owner. Once resolved, remove it from this layer.

### `06-Current-Truth/`
A compact control-tower view of confirmed facts, active unknowns, contradictions being resolved, and current priorities. It should not duplicate full documentation.

### `07-Test-Lab/`
Reusable test plans and fixtures. This is where we design efficient end-to-end tests before running them. Actual results belong in `04-Evidence/`.

### `99-Legacy/`
Temporary holding area for material retained during migration. Nothing in this layer is authoritative.

## Non-duplication rule

A fact should have one canonical owner. Other documents should link to it rather than restating the same detail.
