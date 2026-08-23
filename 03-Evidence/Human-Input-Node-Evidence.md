# Human Input Node Evidence

## Evidence status

**Observed + Documented. Not yet runtime verified.**

## Source 1: UI screenshot supplied in conversation

The supplied Human Input configuration screenshot shows:

- Node title: **HUMAN INPUT 0**
- A **Question** field containing `Please provide your input.`
- A **Save Response As** variable selector, shown with `system / humanInput`
- An **Advanced** section
- **State Update** with `Manage state updates` and `+ Add State`
- **Output Variables (2)**
- Output variable `input`, type `object`, described as `The user's input response`
- Output variable `variableTarget`, type `string`, described as `The flow variable path where input was stored`

## Source 2: Supplied QI Studio / Confluence guidance

The supplied product guidance states that the Human Input node:

> pauses your orchestration to ask a person a question, then saves their answer so the rest of the flow can use it.

It further distinguishes Human Input from Approval:

- Human Input is intended to collect an answer and carry on.
- Approval is intended for a yes/no decision that branches to Approved vs Rejected paths.

The guidance also states that the person is shown the configured Question, the response is saved through Save Response As, and later steps can read the stored response. It notes that an Agent or LLM can use the answer, or a Rule node can branch on it.

## Configuration evidence

### Question

The screenshot establishes a configurable user-facing prompt.

### Save Response As

The screenshot establishes a variable target selector. The example shown is `system / humanInput`.

### Advanced > State Update

The screenshot establishes an optional state-update area. No state update is configured in the captured example.

### Output Variables

The screenshot establishes these outputs:

| Name | Type | Description shown in UI |
|---|---|---|
| `input` | object | The user's input response |
| `variableTarget` | string | The flow variable path where input was stored |

## Evidence boundaries

The screenshot and supplied documentation do **not** by themselves prove:

- exact serialization of `input`
- exact runtime variable path resolution
- creation/overwrite semantics for the target variable
- timing between response persistence and State Update execution
- timeout or abandonment behavior
- resume behavior after a pause
- exact downstream state visibility

These remain verification items rather than established runtime facts.

## Verification status

No independent runtime test has been recorded for this node yet.

See [Human Input Node](../02-Orchestration-Primitives/Human-Input.md) and [Verification Queue](../04-Verification/Verification-Queue.md).
