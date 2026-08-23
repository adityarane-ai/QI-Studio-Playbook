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
- Runtime-confirmed path using Flow state:

```text
Human Input → flow.startTestResponse → explicit Output reference → User Window
```

- Runtime-confirmed path using System state:

```text
Human Input → system.humanInput → explicit Output reference → User Window
```

- The exact response can be observed in node runtime state as `nodes.human_input_0.input` and in the targeted variable.
- See [`14-Evidence/2026-08-23-Start-HumanInput-Output-End-to-End-Tests.md`](../14-Evidence/2026-08-23-Start-HumanInput-Output-End-to-End-Tests.md).

### Output

- The Output node does **not** infer the intended response merely because another node succeeded or because a value exists somewhere in execution state.
- An explicit response source must be configured.
- Runtime-confirmed examples:

```text
{{flow.startTestResponse}}
{{system.humanInput}}
```

- `{{system.humanInput}}` resolved to `START_TEST`, producing `output.messages = START_TEST` and displaying `START_TEST` to the user.
- The complete set of expression types accepted by Output is still not fully mapped.

### Flow Variable path

A Human Input response can target a user-created Flow Variable and the value can be consumed by Output through an explicit variable reference. This is now **Runtime Confirmed**, not merely pending.

What remains unverified is the complete Flow Variable feature set, including all operations and whether all variable-producing nodes behave identically.

### Start node

The START node successfully accepted incoming user messages in the tested flow and exposed the request through runtime state. Its runtime record included the input message, session ID, stream mode, timestamp, node identity, and success status.

**Runtime Confirmed:** Start execution initializes the tested flow path.

**Important boundary:** Start execution success alone does not imply final-response success.

## Current known regression case

An earlier Start + Human Input run completed but the User Window returned:

```text
No response content found in the execution result. Please try again.
```

The controlled tests established that the key issue was the lack of a usable explicit Output response source. The regression is therefore retained as historical evidence that upstream success is not sufficient for user-visible completion.

## Agent node understanding

The Agent node is a configurable semantic execution boundary combining a model, strategy, instructions/messages, tools/capabilities, context controls, memory, state updates, and output variables.

Observed Agent strategy options include `ReAct` and `Deep Agent`. Deep Agent exposes subagent controls, including a visible maximum of 3 parallel subagents in the supplied UI evidence.

Observed Agent advanced areas include Response Format, Include Thoughts, Guardrails, Context Management, Long-term Memory, Error Handling, State Update, and Output Variables.

The Agent output-variable evidence includes at least `text`, `toolCalls`, `structuredOutput`, `success`, and `error` fields.

The Agent UI also exposes capability groups including Tools, Libraries, Skills, Widgets, and Connectors.

See [`02-Orchestration-Primitives/Agent.md`](../02-Orchestration-Primitives/Agent.md) and [`14-Evidence/2026-08-23-Agent-Tool-Catalog.md`](../14-Evidence/2026-08-23-Agent-Tool-Catalog.md).

## Tool catalog captured on 23 Aug 2026

The supplied Agent configuration evidence establishes the following named tools/capabilities:

- Export Excel V2
- Export PowerPoint V2
- Export PDF V2
- Export Word V2
- Export HTML V2
- Extract Document to Markdown
- Brave Web Search
- Set In Memory
- Get From Memory
- Send Email
- Conversation Attachment
- Export File
- Search System Tools
- Get System Tool Schema
- Execute System Tool
- `get-knowledge-workflow-instructions`

The configuration evidence establishes their names, schemas, documented behavior, and declared variable updates. It does **not** by itself prove successful runtime execution of each tool.

See [`14-Evidence/2026-08-23-Agent-Tool-Catalog.md`](../14-Evidence/2026-08-23-Agent-Tool-Catalog.md).

## Current pending questions

1. Prove the complete Flow Variable lifecycle independently of Human Input.
2. Determine whether custom user-created System-scope variables behave like built-in System variables.
3. Determine the exact Output response-source resolution rules and supported expression paths.
4. Determine Agent node output mapping into variables and Output.
5. Determine Approval decision value, routing, and resume semantics.
6. Determine Agent-tool result storage, Variable Path, Store Tool Output, Return Direct, Response Filtering, State Update, and tool-level Human-in-the-Loop semantics.
7. Verify each exported artifact tool through controlled runtime execution, including whether the declared `system.files` append is sufficient for downstream attachment delivery.
8. Verify `ExportBlob` attachment behavior end to end.
9. Verify Web Search citation propagation and the exact runtime shape of returned citations.
10. Verify memory Store/Retrieve persistence scope and lifecycle.
11. Verify Conversation Attachment behavior for one file and multiple files.
12. Verify `get-knowledge-workflow-instructions` ordering and its interaction with knowledge-source tools.
13. Verify Agent Error Handling configuration and recovery semantics.
14. Verify Agent Include Thoughts runtime behavior and downstream exposure.
15. Verify Agent Context Management strategies (`Replace`, `Drop`) and threshold semantics.

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

## Tool testing rule

For system-tool execution through the discovery layer, use:

```text
SearchSystemTools
      ↓
GetSystemToolSchema
      ↓
ExecuteSystemTool
      ↓
Validate node output / variable mapping
      ↓
Validate downstream consumption
```

For knowledge-source workflows, call `get-knowledge-workflow-instructions` first.

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
- `14-Evidence/2026-08-23-Start-HumanInput-Output-End-to-End-Tests.md`
- `14-Evidence/2026-08-23-Agent-Tool-Catalog.md`
