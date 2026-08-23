# High-Information End-to-End Test Suite

Use this small suite instead of dozens of low-value tests. Each test should isolate one concept and inspect both runtime state and user-visible output.

## Test 1: Start -> Human Input -> Output

Purpose: prove the basic pause/resume path and variable wiring.

Configuration:

- Start receives any user message, for example `hello`.
- Human Input asks: `Please enter "START_TEST"`.
- Save response as `flow.startTestResponse`.
- Output references `{{flow.startTestResponse}}`.

Expected evidence:

- Start succeeds.
- Human Input pauses and exposes `resumeUserInput`.
- Submitted text is written to `flow.startTestResponse`.
- Output returns `START_TEST` to the user.

Repeat once with `system.humanInput` as the target and `{{system.humanInput}}` in Output. This pair already established that both namespaces can carry the captured value.

## Test 2: Output wiring failure

Purpose: establish that upstream execution success does not guarantee user-visible output.

Configuration:

- Keep Test 1 identical.
- Remove or break the Output reference to the produced variable.

Expected evidence:

- Upstream Human Input can still succeed.
- Execution can complete.
- User-visible output can fail or show an output-content error because Output has no usable response content.

This is a wiring test, not a node-runtime failure.

## Test 3: Approval branch

Purpose: prove that Approval creates two different downstream routes.

Configuration:

`Start -> Approval -> Approved Path / Rejected Path -> Output`

Use distinct output messages such as `APPROVED_PATH` and `REJECTED_PATH`.

Run once for each button.

Evidence to capture:

- approval prompt
- selected decision
- branch taken
- downstream output
- any output variable written

## Test 4: Compute -> Condition

Purpose: learn state creation and branching together.

Configuration concept:

`Start -> Compute -> Condition -> Output`

Have Compute create one deterministic value, for example a numeric flag or boolean. Condition branches on that produced value.

Run once for the true path and once for the false path.

Evidence to capture:

- produced key
- value and type
- condition expression
- selected branch
- final output

## Test 5: Missing-state behavior

Purpose: discover how branching or compute behaves when a referenced key does not exist.

Configuration:

Reference a key that has not been produced.

Expected result is intentionally not assumed. Record whether the runtime:

- errors
- treats the value as null/empty
- takes a default path
- skips the node
- or produces another behavior

## Decision Tree test

After the standalone nodes are understood, build one minimal internal tree:

`Start -> Ask User -> Compute -> Condition -> Done`

The objective is to verify state propagation inside the Decision Tree rather than to retest the standalone semantics of each node.

## Evidence discipline

For every test capture:

1. exact node configuration
2. exact input
3. exact runtime variable state
4. exact node-level success/failure
5. exact user-visible output

A result is confirmed only when the observed evidence is written into the evidence/canonical layer and the related item is removed from the verification queue.
