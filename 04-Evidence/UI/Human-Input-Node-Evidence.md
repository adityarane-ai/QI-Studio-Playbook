# Human Input Node Evidence

**Evidence ID:** QI-OBS-2026-08-23-HUMAN-INPUT-001  
**Evidence date:** 2026-08-23  
**Status:** Runtime Confirmed for the tested free-text response path.  
**Sources:** User-supplied QI Studio screenshots, supplied product guidance, and controlled runtime tests.

## UI evidence

The Human Input node exposes Question, Save Response As, Advanced → State Update, and Output Variables including `input` and `variableTarget`. The supplied UI example uses `system / humanInput` as a response target.

## Runtime Test A: Flow Variable

```text
Human Input → flow.startTestResponse
Output → {{flow.startTestResponse}}
```

Observed:

```text
nodes.human_input_0.input = "START_TEST"
nodes.human_input_0.variableTarget = "flow.startTestResponse"
flow.startTestResponse = "START_TEST"
nodes.output_0.output.messages = "START_TEST"
status = completed
```

**PASS: Runtime Confirmed.**

## Runtime Test B: System `humanInput`

```text
Human Input → system.humanInput
Output → {{system.humanInput}}
```

Observed:

```text
nodes.human_input_0.input = "START_TEST"
nodes.human_input_0.variableTarget = "system.humanInput"
system.humanInput = "START_TEST"
nodes.output_0.output.messages = "START_TEST"
User Window = "START_TEST"
status = completed
```

**PASS: Runtime Confirmed.**

## Established behavior

The tested Human Input node pauses for a free-text response, exposes the captured response in node runtime data, writes it to the configured tested target variable, and allows downstream Output to consume that target through an explicit variable reference.

## Historical regression

An earlier flow completed upstream but the User Window showed:

```text
No response content found in the execution result. Please try again.
```

Later controlled tests establish that the important distinction is the **Output response contract**. Upstream Human Input success does not itself guarantee a user-visible final response.

## Evidence boundary

Still not runtime-proven:

- exact serialization of output variable `input` for all response types;
- target-variable automatic creation/overwrite semantics outside tested paths;
- Human Input State Update ordering;
- timeout, abandonment and resume behavior;
- widget or other response modes;
- full Output expression-language coverage.

Keep the failed regression as historical evidence. Do not interpret it as proof that Human Input itself fails.
