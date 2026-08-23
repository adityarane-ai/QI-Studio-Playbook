# Flow Variables, System State, and Output Wiring

This is one of the most important implementation concepts established by the early end-to-end tests.

## Two variable namespaces observed

### Flow variables

Flow variables are created separately in the UI using the Variables mechanism and are available under:

`flow.<variableName>`

Example:

`flow.startTestResponse`

### System variables

System state is exposed under the `system` namespace. One directly observed value is:

`system.humanInput`

The test also showed system context containing values such as `system.userQuery`, `system.attachments`, `system.files`, `system.sessionId`, `system.dateTime.utcNow`, and resume/checkpoint information.

## Human Input target

Human Input writes the captured response into the variable target configured on the node.

Two confirmed configurations were tested:

1. `flow.startTestResponse`
2. `system.humanInput`

The resulting runtime state matched the configured target.

## Output wiring

A node execution may succeed while the user-visible output is still wrong or empty if the Output node is not configured to reference the desired variable.

Therefore every end-to-end test must validate both:

- the variable state produced by the upstream node
- the actual user-visible Output result

### Confirmed working patterns

```text
Start
  -> Human Input (writes flow.startTestResponse)
  -> Output {{flow.startTestResponse}}
```

and

```text
Start
  -> Human Input (writes system.humanInput)
  -> Output {{system.humanInput}}
```

Both produced:

`START_TEST`

## Resume behavior observed

When Human Input pauses execution, the runtime state includes a `resumeUserInput` object describing:

- `hitlType: human_input`
- the prompt message
- `variableTarget`

The runtime also exposes `resumeContext` including checkpoint and interrupt information.

After the user submits the answer, the captured value appears in the configured target.

## Documentation rule

Do not treat `system.humanInput` as a replacement for flow variables in general. It is one observed system-state destination. Use a flow variable when the workflow requires an explicitly named business/workflow state variable that should be created and managed through the Variables UI.
