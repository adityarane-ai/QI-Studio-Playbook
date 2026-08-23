# HUMAN INPUT Node

## Status

**Observed + Documented. Runtime behavior not yet independently verified.**

## Purpose

The **Human Input** node pauses an orchestration, asks a person a question, captures the person's response, and stores that response so downstream steps can use it.

Use Human Input when the workflow needs a person's free-form input. Use **Approval** when the workflow needs an explicit approve/reject decision with separate routing paths.

## Configuration

### Question

The **Question** field contains the prompt shown to the person.

Example default:

```text
Please provide your input.
```

The prompt should be specific enough that the person knows exactly what information to provide.

### Save Response As

The node provides a **Save Response As** variable selector. The selected variable is where the person's response is stored.

The supplied UI example shows a system-scoped variable:

```text
system / humanInput
```

This is a configurable variable target, not necessarily a universal fixed path.

## Advanced configuration

### State Update

The Advanced section exposes **State Update** with the ability to add state mutations. The supplied screenshot shows no variable updates configured by default.

Runtime ordering and interaction between these state updates and the captured input remain unverified.

### Output Variables

The supplied UI exposes two output variables:

| Output | Type | Meaning |
|---|---|---|
| `input` | object | The user's input response |
| `variableTarget` | string | The flow variable path where the input was stored |

The exact runtime serialization of `input` is not yet independently verified.

## Conceptual execution

```mermaid
flowchart LR
    A[Workflow reaches Human Input] --> B[Show Question to person]
    B --> C[Wait for response]
    C --> D[Save response to selected variable]
    D --> E[Continue downstream workflow]
```

## Examples

### Free-form information collection

```text
Question:
Which supplier should be invited to the next RFP?

Save Response As:
system / humanInput
```

A downstream Agent or LLM can use the captured response to continue the workflow.

### Collecting a business explanation

```text
Question:
Please explain why this supplier should be excluded.
```

The response can then be consumed by a downstream processing node or used by a Rule/Decision mechanism.

## Human Input vs Approval

| Requirement | Human Input | Approval |
|---|---|---|
| Free-form response | Yes | No, primary purpose is decision |
| Explicit approve/reject choice | No | Yes |
| Two separate routes | Not established by the node itself | Yes, Approved and Rejected |
| Stores person's response | Yes | Yes, through decision output/state |

## What the supplied evidence establishes

- Human Input is a dedicated node that asks a person a question.
- The person responds and the response is saved to a selected variable target.
- The node exposes Advanced > State Update.
- The node exposes output variables `input` and `variableTarget`.
- The documented guidance distinguishes Human Input from Approval.

## What remains unverified

- Exact persisted shape of `input` for different response types.
- Exact variable-path resolution and scoping behavior.
- Whether the selected variable is created automatically when absent.
- Behavior when a target variable already contains a value.
- State Update ordering relative to response persistence.
- Timeout, abandonment, and resume behavior.
- Whether downstream execution sees the captured value immediately and in what exact representation.

See [Human Input Node Evidence](../03-Evidence/Human-Input-Node-Evidence.md) and the [Verification Queue](../04-Verification/Verification-Queue.md).
