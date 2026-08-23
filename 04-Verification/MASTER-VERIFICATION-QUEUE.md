# Master Verification Queue

This is the active queue of claims that still require direct evidence. Once a claim is confirmed, move the confirmed detail into the appropriate canonical/evidence document and remove it from this queue.

## Verification status model

- **UNKNOWN**: no reliable evidence yet.
- **PARTIAL**: some behavior is known, but important details remain untested.
- **CONFIRMED**: demonstrated by a targeted runtime test or explicit product configuration/evidence.
- **CONFLICT**: two credible sources disagree; do not resolve by assumption.

## Current queue

| Area | Claim to verify | Status | Best next test |
|---|---|---|---|
| Output | Exact behavior when the referenced variable is missing/null | PARTIAL | Produce a missing key and observe rendered output |
| Output | Whether arbitrary expressions are accepted or only supported variable references | UNKNOWN | Test several reference forms |
| Human Input | Whether any restrictions exist on permitted target namespaces beyond what UI exposes | PARTIAL | Test flow and system targets already used, then an invalid target |
| Human Input | Exact persistence semantics after pause/resume across a new turn | PARTIAL | Repeat the same Human Input test after resume |
| Approval | Exact output variable shape and persistence semantics | UNKNOWN | Approve and reject branches with explicit outputs |
| Approval | Whether rejection always terminates or can continue through a connected branch | PARTIAL | Connect Rejected to an explicit downstream node and test |
| Condition | Exact expression grammar and supported operators | UNKNOWN | Test equality, inequality, boolean combinations, and missing keys |
| Compute | Exact expression language and type/coercion rules | UNKNOWN | Run arithmetic, string, boolean, null, and invalid expressions |
| Decision Tree | Exact state propagation between internal steps | PARTIAL | Build a minimal Ask -> Compute -> Condition -> Done tree |
| Agent tools | Tool discovery/execution protocol and whether search/schema calls are mandatory at runtime | PARTIAL | Execute a system action through Search -> Schema -> Execute |
| Export tools | Exact returned file object shape for every export family | PARTIAL | Run one controlled export per format and inspect output |
| Knowledge workflow | Whether `get-knowledge-workflow-instructions` is mandatory on every knowledge-source invocation | CONFIRMED by supplied tool contract, runtime behavior still to test | Run one knowledge query with and without initialization |

## Evidence rule

Do not delete an entry merely because the behavior appears obvious. Delete it only when the evidence is recorded in the canonical/evidence layer.
