# Current Understanding & Verification Ledger

**Status:** Canonical working ledger  
**Last updated:** 23 Aug 2026  
**Scope:** Current QI Studio platform understanding established through supplied UI/documentation evidence and runtime tests.

This ledger is the authoritative place for facts that are currently believed to be true but may still require verification. It prevents working assumptions from being mixed with established evidence.

## Evidence status model

| Status | Meaning |
|---|---|
| Confirmed | Directly shown in QI Studio UI or explicit platform/product documentation. |
| Runtime Confirmed | Demonstrated through an actual execution and downstream/user-visible result. |
| Working Understanding | Strong interpretation based on evidence, but not fully proven. |
| Pending Verification | Capability/question identified but not sufficiently tested. |
| Contradicted | An earlier assumption was disproven by stronger evidence. |
| Superseded | An earlier formulation remains historically useful but has been replaced by newer evidence. |

## Current verified understanding

### Variables and state

- QI Studio exposes logical variable areas including **Flow**, **System**, **Conversation History**, **Runtime**, and an aggregate view.
- Flow Variables are workflow-owned state and are created through the Variables UI.
- Built-in System variables are platform-managed. The tested `system.humanInput` variable is populated by Human Input.
- Conversation History is conversational context and should not be used as a substitute for explicit workflow state.
- Runtime variables describe execution context and should not be treated as business state.

### Human Input

- Human Input pauses the orchestration, asks a person a free-text question, captures the response, and stores it in a configured variable target.
- Runtime-confirmed path:

```text
Human Input → system.humanInput → explicit Output reference → User Window
```

- The exact response can be observed in node runtime state as `nodes.human_input_0.input` and in the targeted variable.

### Output

- The Output node does **not** infer the intended response merely because another node succeeded or because a value exists somewhere in execution state.
- An explicit response source must be configured.
- Runtime-confirmed example:

```text
{{system.humanInput}}
```

- That expression resolved to `START_TEST`, producing `output.messages = START_TEST` and displaying `START_TEST` to the user.
- The complete set of expression types accepted by Output is still not fully mapped.

### Flow Variable path

A Human Input response can target a user-created Flow Variable. Earlier testing showed the Flow state populated successfully, but the isolated Flow Variable → Output linkage should remain a dedicated verification case until reconfirmed under controlled conditions.

### Start node

The START node successfully accepted an incoming user message in the tested flow and exposed the request through the system/runtime state. START execution success alone does not imply final-response success.

### Evidence principle

A value appearing in execution JSON is not sufficient to mark a data path runtime-confirmed. The intended path must work through downstream consumption and, where relevant, the actual User Window.

## Current known regression case

Earlier configuration:

```text
START → Human Input
```

The workflow completed, but the User Window returned:

```text
No response content found in the execution result. Please try again.
```

The current controlled evidence indicates the key issue was absence of an explicit Output response source. Keep this execution as regression evidence rather than as the universal behaviour of the Output node.

## Current pending questions

1. Prove the complete Flow Variable lifecycle independently of Human Input.
2. Prove `{{flow.<name>}}` as an Output response source in an isolated controlled test.
3. Determine whether custom user-created System-scope variables behave like built-in System variables.
4. Determine the exact Output response-source resolution rules and supported expression paths.
5. Determine Agent node output mapping into variables and Output.
6. Determine Approval decision value, routing, and resume semantics.
7. Determine Agent-tool result storage, Variable Path, Store Tool Output, Return Direct, Response Filtering, State Update and tool-level Human-in-the-Loop semantics.
8. Verify remaining tool-specific runtime behavior before treating UI configuration as complete platform knowledge.

## Canonical testing rule

Use four layers for every capability:

```text
UI contract
   ↓
Runtime contract
   ↓
Downstream contract
   ↓
User-visible contract
```

Only the fourth layer, when applicable, can establish full end-to-end behavior.

## Lifecycle of this ledger

When a pending item is verified:

```text
Pending Verification
      ↓
Runtime test / direct evidence
      ↓
Update canonical documentation + evidence record
      ↓
Remove the item from the pending section
      ↓
Retain the historical test in the test log
```

Do not silently delete failed or superseded tests. Historical failures explain why the current design exists.

## Related canonical documents

- `02-Orchestration-Primitives/Start.md`
- `02-Orchestration-Primitives/Human-Input.md`
- `02-Orchestration-Primitives/Agent.md`
- `04-Verification/Verification-Queue.md`
- `12 - End-to-End Node Test Log.md` where available from legacy working documents
