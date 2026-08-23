# Human Input Node

> **Evidence status:** Runtime Confirmed for the tested free-text path.
> **Evidence date:** 2026-08-23
> **Primary evidence:** User-supplied QI Studio Human Input screenshots plus two controlled end-to-end runtime tests.

The **Human Input** node pauses an orchestration, asks a person a question, captures the response, and stores it in a configured variable target so downstream steps can use it.

Use Human Input for free-form information collection. Use **Approval** for a binary human authorization checkpoint with Approved and Rejected routing.

## Configuration

### Question

The **Question** field controls the prompt shown to the person. The question should state exactly what information the workflow needs.

### Save Response As

The node lets the designer choose where the response is stored. A system-scoped example is `system.humanInput`. A controlled runtime test also confirmed a user-created Flow Variable target, `flow.startTestResponse`.

## Runtime-confirmed behavior

### Flow Variable path

```text
Human Input → flow.startTestResponse → Output {{flow.startTestResponse}}
```

Observed:

```text
flow.startTestResponse = "START_TEST"
nodes.human_input_0.input = "START_TEST"
nodes.output_0.output.messages = "START_TEST"
```

### System Variable path

```text
Human Input → system.humanInput → Output {{system.humanInput}}
```

Observed:

```text
system.humanInput = "START_TEST"
nodes.human_input_0.input = "START_TEST"
nodes.output_0.output.messages = "START_TEST"
User Window = "START_TEST"
```

These tests establish that the captured response is available to downstream Output through an explicit variable reference.

## Advanced configuration

The Advanced section exposes **State Update** and **Output Variables**. The supplied UI shows `input` and `variableTarget` outputs. The exact runtime serialization of `input` and the ordering between optional state updates and response persistence remain unverified.

## Human Input vs Approval

| Requirement | Human Input | Approval |
|---|---|---|
| Free-form response | Yes | No, primary purpose is binary authorization |
| Explicit approve/reject choice | Not the intended primitive | Yes |
| Separate Approved / Rejected routes | Not established by Human Input | Yes |
| Stores a human response/decision | Yes | Yes |

## Output rule

Human Input can complete successfully without producing a user-visible final response. The workflow must provide the Output node with an explicit usable response source. A historical regression returned:

```text
No response content found in the execution result. Please try again.
```

## Still unverified

- Complete Output expression-language coverage.
- Exact persisted shape of `input` for every response mode.
- Automatic creation and overwrite behavior for untested target variables/scopes.
- State Update ordering relative to response persistence.
- Timeout, abandonment and resume behavior.
- Behavior across other Human Input response modes.

## Evidence

See `04-Evidence/Runtime/Start-HumanInput-Output-E2E.md` and `04-Evidence/UI/Human-Input-Node-Evidence.md`.
