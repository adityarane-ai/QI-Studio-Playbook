# Variable Update Node Evidence Record

**Evidence ID:** QI-OBS-2026-08-23-VARIABLE-UPDATE-001  
**Date:** 2026-08-23  
**Capability:** Explicit workflow-state mutation, update operations, conditional updates, state-update configuration.  
**Source:** User-supplied QI Studio Variable Update screenshots and accompanying product guidance.

## What was observed / documented

1. The node is identified as `VARIABLE 0` / Variable Update.
2. The node description states that it writes to the orchestration's memory through shared state variables.
3. The node exposes **Manage state updates**.
4. A **State updates configured** counter is displayed.
5. `+ Add State` adds another update row.
6. Each update row contains a variable selector.
7. Each update row contains an operation selector.
8. Each update row contains a value field.
9. The value field supports fixed values and variable insertion using `{{`.
10. The supplied documentation explicitly describes **Set** as replacing the variable's value.
11. The node exposes **+ Run only when** for conditional execution.
12. Conditional execution exposes **ALL** and **ANY** modes.
13. The condition builder exposes **+ Add Condition**.
14. The visible condition operators are:
    - Equals
    - Not Equals
    - Greater Than or Equal To
    - Less Than or Equal To
    - Greater Than
    - Less Than
    - Contains
    - Is Empty
    - Is Not Empty
15. By default, a Variable Update executes when the node is reached; conditional mode restricts execution to matching conditions.

## Product-guidance semantics captured

The supplied guidance explicitly states that state is shared memory carried through the orchestration and that Variable Update is the explicit way to write or change that state.

It also states that other nodes can modify state through their Advanced -> State Update settings, while Variable Update should be used when the state change itself is the explicit and visible purpose of the step.

## What the screenshots do not prove

The evidence does not establish complete runtime semantics for:

- type coercion in conditional comparisons
- null vs missing vs empty-string behavior
- transactionality when multiple state rows are configured
- behavior when a later update fails after an earlier update succeeds
- exact variable-scope resolution precedence
- nested/complex Boolean grouping beyond the visible ALL/ANY control

## Recommended follow-up tests

| ID | Test | Purpose |
|---|---|---|
| VAR-001 | Set a string variable | Confirm replacement semantics |
| VAR-002 | Set a number with numeric input | Confirm type preservation |
| VAR-003 | Compare number against string | Test coercion behavior |
| VAR-004 | Missing field with Equals | Determine missing-value behavior |
| VAR-005 | Empty string with Is Empty | Determine empty semantics |
| VAR-006 | Null with Is Empty | Determine null semantics |
| VAR-007 | ALL with two conditions | Confirm AND semantics |
| VAR-008 | ANY with two conditions | Confirm OR semantics |
| VAR-009 | Three state updates in one node | Test multi-update behavior |
| VAR-010 | Force failure after first mutation | Determine atomicity / partial-update behavior |
| VAR-011 | Reference upstream Script output | Verify variable-path resolution |
| VAR-012 | Branch-specific state update | Confirm Run only when integration with Rule branches |

## Evidence discipline

Do not store credentials, runtime authorization values, tokens, or other secrets in evidence screenshots or repository documentation. Redact sensitive values before preserving evidence.
