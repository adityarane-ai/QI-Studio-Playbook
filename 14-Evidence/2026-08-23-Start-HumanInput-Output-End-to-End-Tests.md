# Start + Human Input + Output End-to-End Tests

**Test date:** 2026-08-23  
**Status:** RUNTIME CONFIRMED for the paths explicitly described below.

## Test objective

Establish the smallest reliable end-to-end contract connecting:

```text
START
  ↓
HUMAN INPUT
  ↓
variable target
  ↓
OUTPUT explicit reference
  ↓
User Window
```

The tests were intentionally minimal so that failures can be attributed to the data path rather than to an Agent, Tool, Rule, Decision Tree, or other unrelated node.

---

## Test 1: Human Input → Flow Variable → Output

### Configuration

```text
START
  ↓
Human Input
  Target: flow.startTestResponse
  Prompt: Please enter "START_TEST"
  ↓
Output
  Response source: {{flow.startTestResponse}}
```

### Input

Initial user message:

```text
hello
```

Human response:

```text
START_TEST
```

### Runtime evidence

The execution state contained:

```json
"flow": {
  "startTestResponse": "START_TEST"
}
```

Human Input node:

```json
{
  "input": "START_TEST",
  "variableTarget": "flow.startTestResponse",
  "success": true
}
```

Output node:

```json
{
  "output": {
    "messages": "START_TEST"
  },
  "success": true
}
```

### Result

**PASS.** A Human Input response can be written to a user-created Flow Variable and explicitly consumed by Output.

### What this proves

- Flow Variables are real workflow state and can receive Human Input.
- Human Input records the response in its node runtime state.
- Output can resolve a Flow Variable reference when that reference is configured explicitly.
- The final output can be user-visible when the path is explicitly connected.

### What this does not prove

It does not prove every Flow Variable operation, every scope, or every expression accepted by Output.

---

## Test 2: Human Input → System Variable → Output

### Configuration

Everything was kept identical to Test 1 except:

```text
Human Input target: system.humanInput
```

Output:

```text
{{system.humanInput}}
```

### Input

Initial user message:

```text
hello
```

Human response:

```text
START_TEST
```

### Runtime evidence

The execution state contained:

```json
"system": {
  "humanInput": "START_TEST"
}
```

Human Input node:

```json
{
  "input": "START_TEST",
  "variableTarget": "system.humanInput",
  "success": true
}
```

Output node:

```json
{
  "output": {
    "messages": "START_TEST"
  },
  "success": true
}
```

User Window output:

```text
START_TEST
```

### Result

**PASS. Runtime Confirmed.** `system.humanInput` is populated by Human Input and can be explicitly referenced by Output.

This is currently the strongest direct proof of the Human Input → System Variable → Output path.

---

## Regression case: missing/incorrect Output response source

An earlier test used a Start + Human Input flow where the downstream Output response source was not configured to consume the required variable.

The execution itself completed successfully, but the user-facing window returned:

```text
No response content found in the execution result. Please try again.
```

### Interpretation

This is not evidence that Human Input failed.

It is evidence that **successful upstream execution does not automatically create a valid final response**.

The downstream Output node must be configured with an explicit response source.

This failure became explainable after the explicit-response-source tests above succeeded.

---

## Start node observations from these tests

The Start node runtime record contained fields including:

- `marker: START`
- `interface.inputs.message`
- `options.sessionId`
- `options.streamMode`
- `timestamp`
- `nodeId`
- `nodeType`
- `name`
- `type`
- `success`

Example input values observed:

```json
"interface": {
  "inputs": {
    "message": "hello"
  }
}
```

### Start-node conclusion

**Runtime Confirmed:** Start successfully accepts the incoming user message and initializes the execution path.

**Important boundary:** Start success alone is not equivalent to successful final-response delivery.

---

## Flow Variables vs System Variables

The two successful tests establish a useful distinction:

```text
Flow
└── startTestResponse = START_TEST

System
└── humanInput = START_TEST
```

Both can hold values consumed by Output when referenced explicitly.

The tests do not establish that user-created Flow Variables and built-in System Variables are interchangeable in all contexts.

---

## Current Output contract

The strongest confirmed operational rule is:

> Output needs an explicit source/reference for the response it should return.

Examples proven in these tests:

```text
{{flow.startTestResponse}}
{{system.humanInput}}
```

Therefore a safe baseline test pattern is:

```text
START
  ↓
Human Input
  ↓
write response to known variable
  ↓
Output references that exact variable
```

---

## Minimal future regression test

For any change to Start, Human Input, variables, or Output, rerun this small test first:

1. Start with `hello`.
2. Human Input asks for `START_TEST`.
3. Write to `system.humanInput`.
4. Output uses `{{system.humanInput}}`.
5. Confirm User Window displays `START_TEST`.

If this fails, do not proceed to more complex Agent/Tool testing until the basic state-to-output contract is restored.

---

## Verification status table

| Capability | Status | Evidence |
|---|---|---|
| Start accepts user message | Runtime Confirmed | Start node runtime state |
| Human Input pauses and captures reply | Runtime Confirmed | `nodes.human_input_0.input` |
| Human Input writes to Flow Variable | Runtime Confirmed | `flow.startTestResponse` |
| Human Input writes to `system.humanInput` | Runtime Confirmed | `system.humanInput` |
| Output consumes Flow Variable explicitly | Runtime Confirmed | Test 1 |
| Output consumes `system.humanInput` explicitly | Runtime Confirmed | Test 2 |
| Output automatically infers a final response | **Not supported by evidence** | Regression disproves assumption |
| Full Output expression language | Pending Verification | Broader mapping required |
