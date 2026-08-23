# QI Studio Playbook: Repository Contract

This repository is the working source of truth for understanding, testing, and documenting QI Studio orchestration.

## Canonical documentation model

The repo separates four things that must never be silently mixed:

1. **Canonical Reference**: what the product/tool configuration explicitly says.
2. **Evidence**: what has been directly observed from screenshots, runtime outputs, or supplied tool definitions.
3. **Current Understanding**: the best working interpretation derived from the evidence.
4. **Verification Queue**: claims that are still uncertain and require a targeted test or product confirmation.

A claim moves from Verification Queue to Evidence/Canonical Reference only after the required evidence exists. We do not convert assumptions into facts merely because they appear plausible.

## Current structural rule

Use the newly structured numbered areas as the active organization. Existing duplicate or historical areas are retained only until their content has been reconciled. They are not independent sources of truth.

### Active areas

- `01-Governance/`: documentation and evidence rules.
- `02-Orchestration-Primitives/`: node behavior and orchestration concepts.
- `03-Canonical-Reference/`: normalized product/tool contracts.
- `03-Evidence/` and `04-Evidence/`: historical evidence collections pending consolidation.
- `04-Verification/` and `05-Verification/`: verification queues pending consolidation.
- `05-Current-Understanding/` and `06-Current-Truth/`: working interpretations pending consolidation.
- `07-Test-Lab/`: targeted end-to-end tests and observed results.
- `99-Legacy/`: material retained for traceability only.

## Hard rules

- Do not invent node properties that have not been observed.
- Do not copy the same fact into multiple authoritative documents.
- When two sources disagree, record the conflict and prefer direct runtime evidence over inference.
- Keep tool schemas and node behavior separate. A tool's declared schema is not proof of runtime behavior beyond what has been tested.
- Test variables explicitly. Flow variables and system variables are distinct concepts and references must preserve that distinction.
- A successful node execution is not enough to prove that the final Output node is correctly wired. Verify the actual user-visible output.

## Current confirmed lessons from end-to-end testing

### Start node

The Start node creates the initial execution context and exposes the inbound user message in the runtime state. A Start node itself does not need a configurable "done message" for the test pattern used so far.

### Human Input node

Human Input pauses execution, asks the user for free-text input, and writes that response to the selected variable target.

Two targets have been directly observed:

- `flow.startTestResponse`
- `system.humanInput`

Both can hold the submitted response. The difference is where the value lives, not whether the Human Input node captured it.

### Output node

The Output node must be explicitly configured to reference the intended variable or output expression. A previous test demonstrated that execution can complete successfully while the final response fails to render because Output is not wired to the produced value.

A working pattern was:

`Human Input -> flow.startTestResponse -> Output {{flow.startTestResponse}}`

and equivalently:

`Human Input -> system.humanInput -> Output {{system.humanInput}}`

In both cases the observed user-visible output was `START_TEST`.

## Testing philosophy

Prefer a small number of high-information tests over a large checklist. Each test should isolate one behavior and produce evidence that also informs implementation decisions.
