# Start → Human Input → Output: End-to-End Evidence

**Evidence date:** 2026-08-23  
**Status:** Runtime Confirmed  
**Purpose:** Canonical record of the smallest tests that establish the Start, Human Input, Flow/System variable, and Output contract.

## Test 1: Human Input writes to a Flow Variable

```text
START
  ↓
Human Input
  Save Response As → flow.startTestResponse
  ↓
Output
  Response source → {{flow.startTestResponse}}
```

Initial user message: `hello`  
Human response: `START_TEST`

Observed: Start succeeded; Human Input captured `START_TEST`; `flow.startTestResponse` contained `START_TEST`; Output emitted `START_TEST`; overall status was `completed`.

**PASS: Runtime Confirmed.**

## Test 2: Human Input writes to System `humanInput`

```text
START
  ↓
Human Input
  Save Response As → system.humanInput
  ↓
Output
  Response source → {{system.humanInput}}
```

Initial user message: `hello`  
Human response: `START_TEST`

Observed: Start succeeded; Human Input captured `START_TEST`; `system.humanInput` contained `START_TEST`; Output emitted `START_TEST`; User Window output was `START_TEST`; overall status was `completed`.

**PASS: Runtime Confirmed.**

## Regression: no explicit Output source

An earlier workflow let Start and Human Input execute successfully but did not configure a usable Output response source. The User Window returned:

```text
No response content found in the execution result. Please try again.
```

Interpretation: this is an **Output contract failure**, not a Human Input failure. Upstream success does not guarantee a user-visible final response.

## Established contract

```mermaid
flowchart LR
    S[Start] --> H[Human Input]
    H --> V[Target variable]
    V --> O[Output: explicit response reference]
    O --> U[User Window]
```

Proven response sources:

```text
{{flow.startTestResponse}}
{{system.humanInput}}
```

## What is proven

- Start executes for the tested invocation path.
- Human Input captures a free-text response.
- Human Input can write the tested response to a Flow Variable.
- Human Input can write the tested response to `system.humanInput`.
- Output consumes explicit variable references.
- Upstream success does not guarantee a user-visible response.

## Open

- Complete Output expression-language coverage.
- Human Input `input` output shape across response modes.
- Variable creation/overwrite behavior beyond tested cases.
- Human Input timeout, abandonment and resume semantics.
- General Start input schema beyond current observed fields.

Historical failures remain valid evidence and should not be deleted merely because the configuration was corrected.
