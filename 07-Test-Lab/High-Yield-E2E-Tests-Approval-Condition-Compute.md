# High-Yield E2E Tests: Approval, Condition, Compute

**Status:** Test designs only. Execute in QI Studio before marking runtime-confirmed.

These tests are deliberately small in number and maximize understanding by isolating the core contract of each node and then testing one composition pattern.

## Test 1: Approval routes correctly

### Goal
Establish the basic Approved versus Rejected routing contract.

### Flow

```text
Start
  ↓
Approval
  ├── Approved → Output
  └── Rejected → Output
```

### Setup

Approval message:
`Approve this test action?`

Button labels:
`Approve` / `Reject`

Configure distinct Output messages on the two branches, for example:

- Approved path: `APPROVAL_APPROVED`
- Rejected path: `APPROVAL_REJECTED`

### Run
Execute twice, once choosing Approve and once choosing Reject.

### Record
Capture the complete runtime envelope, including node outputs, state variables, resume information, and final Output content.

### Questions answered

- Does the node pause execution?
- Does exactly one route continue after the human decision?
- Are the two routes reliably distinguishable?
- What state/output representation is produced for the decision?

---

## Test 2: Condition branches on a known state value

### Goal
Establish how Condition reads a state variable and routes based on its value.

### Flow

```text
Start
  ↓
Human Input
  ↓
Condition
  ├── match → Output A
  └── no-match → Output B
```

### Setup
Create a flow variable:
`flow.conditionTestValue`

Human Input writes to that variable.

Condition checks whether:
`flow.conditionTestValue == "YES"`

Use distinct outputs:

- match: `CONDITION_TRUE`
- no-match: `CONDITION_FALSE`

### Run
Execute twice with `YES` and `NO`.

### Record
Capture the runtime envelope and the exact configured condition syntax.

### Questions answered

- What expression syntax does Condition accept?
- Does it read the expected flow variable?
- Does the false path execute correctly?
- What happens when the input is an unexpected value?

---

## Test 3: Compute produces a value consumed by Condition

### Goal
Verify state transformation plus downstream consumption in one composition test.

### Flow

```text
Start
  ↓
Human Input
  ↓
Compute
  ↓
Condition
  ├── high → Output A
  └── low  → Output B
```

### Setup
Create:
`flow.computeInput`

Human Input captures a numeric test value.

Compute creates:
`flow.computeResult`

Use a simple deterministic transformation such as:
`computeResult = computeInput * 2`

Condition evaluates:
`flow.computeResult >= 20`

Distinct outputs:

- high: `COMPUTE_CONDITION_HIGH`
- low: `COMPUTE_CONDITION_LOW`

### Run
Use at least two inputs that land on different sides of the threshold, such as `5` and `10`.

### Record
Capture all state before and after Compute, the Compute node output, the Condition routing, and the final Output.

### Questions answered

- Can Compute read a flow variable written by Human Input?
- How is the Compute result written to state?
- Can Condition immediately consume that result?
- How are numeric values typed/coerced?

---

## Test 4: Missing-state edge case

### Goal
Understand failure behavior when a Condition or Compute references a state value that is absent.

### Flow

Use a minimal flow in which the referenced variable is never created.

```text
Start → Condition → Output
```

or

```text
Start → Compute → Output
```

### Record
Capture whether the node errors, treats the value as null/empty, skips, or continues.

This test is important because missing-state behavior affects safe production design.

---

## Evidence standard

For every execution, retain:

1. Flow configuration or screenshot sufficient to reproduce the test.
2. Input supplied by the tester.
3. Complete runtime result.
4. Final Output content.
5. Any `variables`, `nodes`, `resumeContext`, and `resumeUserInput` data relevant to the claim.

Do not mark a behavior as **Runtime Confirmed** from UI appearance alone.

## Promotion rule

After execution:

- update `04-Evidence/` with the raw observed result;
- update the corresponding canonical reference only with behavior actually demonstrated;
- remove resolved questions from `05-Verification/`;
- update `06-Current-Truth/` with the resulting high-confidence conclusion.
