# Bid Analysis Agent — QI Studio Configuration Surface

**Evidence date:** 2026-08-24  
**Status:** Observed UI / configuration evidence; runtime semantics remain open unless separately tested.  
**Related canonical references:** `03-Canonical-Reference/Orchestrations/Bid-Analysis-Agent-Architecture.md`, `Bid-Analysis-Agent-Handoff-Contracts.md`, `Bid-Analysis-Agent-State-Contracts.md`

## Purpose

Record the QI Studio configuration surfaces observed while building the Bid Analysis Agent. This document records what was visible and configured in the product UI. It does not claim that every configuration behaves as intended at runtime.

## Start node

Observed Start configuration surface includes:

- API invocation as the always-available trigger.
- Optional Scheduler trigger.
- Optional Event trigger.
- Inputs:
  - `message` — required string.
  - `attachments` — array.
  - `ui_action` — object.
- Advanced State Update surface supporting Set/Clear/Append-style operations and conditional execution.
- Runtime/system mappings used in the Bid Analysis build include:
  - `system.userQuery ← message`
  - `system.attachments ← attachments`
  - `system.uiAction ← ui_action`
  - `system.sessionId ← Current Node / options.sessionId`
  - `flow.runId ← system.sessionId`
  - `flow.currentStage ← "intake"`

The build deliberately avoids clearing `system.files` at Start because uploaded/runtime file context is required by downstream discovery.

## Agent node

Observed Agent configuration surface includes:

- Agent Strategy, including `Deep Agent`.
- Model selection, with `Claude Opus 4.6` used for this build.
- System and User messages.
- Tools.
- Libraries.
- Skills.
- Widgets.
- Connectors.
- Response Format (`Plain Text` / `JSON`).
- Include Thoughts.
- Guardrails.
- Context Management.
- Long-term Memory.
- Error Handling.
- State Update.
- Output Variables.

Baseline used during construction:

```text
Strategy: Deep Agent
Response Format: JSON
Include Thoughts: OFF
Context Management: ON
  Scope: Tool results only
  Strategy: Replace
  Threshold unit: Tokens
Long-term Memory: OFF
Error Handling: OFF
```

These settings are configuration choices for the current build, not runtime-proven semantics.

## Agent tool surface

A Master Deep Agent was observed with Handoff tools and knowledge tools attached simultaneously. Handoff tools expose a dedicated tool configuration surface with:

- Tool Name
- Description
- Typed Tool Parameters
- Handoff Target Node
- State Update
- Advanced options
- Human-in-the-Loop

Typed parameter options observed include:

- string
- number
- boolean
- object
- array<string>
- array<number>
- array<boolean>
- array<object>
- array<array>

`Configure values` exposes a per-parameter `Let agent decide the value` switch.

This is being used to distinguish dynamic delegation parameters from fixed output contracts.

## Handoff return edge

A Deep Agent return edge exposes `Edge settings` with:

- Pass context toggle.
- Transfer type:
  - `Last message (append)`
  - `Full conversation (extend)`
  - `Replace conversation (set)`

The current Bid Analysis build uses:

```text
Pass context: ON
Transfer type: Last message (append)
```

for specialist returns. This is a current build baseline and requires runtime proof for exact serialization and context semantics.

## Human Input

Observed Human Input surface includes:

- Question
- Save Response As
- Advanced
- State Update
- Output Variables

The Bid Analysis build uses the native `system.humanInput` target to capture the immediate response. `flow.evaluationConfiguration` is intentionally not written directly by Human Input; Configuration Validation is intended to create the authoritative configuration after the response is received.

The existing separate runtime evidence establishes that `system.humanInput` can carry a Human Input response downstream in a tested path.

## Script node

Observed Script configuration surface includes:

- typed Inputs
- Visual Editor / JSON Schema
- JavaScript code editor
- Test Script
- typed Return (Output) schema
- Advanced → Error Handling
- Advanced → State Update
- native Output Variables

The Script UI itself displays that script results can be consumed via node result expressions. The Bid Analysis build uses explicit expressions such as:

```text
{{nodes.ranking.result}}
{{nodes.configuration_validation.result.evaluationConfiguration}}
```

for state mapping.

## Output node

Observed Output configuration includes:

- Render Images toggle.
- Output Mapping.
- default `messages` mapping.
- `selectedAgentId`.
- `selectedAgentName`.
- Custom output variables.
- Report Session Outcome toggle.

The Bid Analysis build is changing the default Human Input message mapping so that final output can come from the Execution & Reporting Agent and report metadata can be exposed separately.

## Skill surface

Observed Skill Editor includes:

- Definition section containing Name, Description and Instructions.
- Tools section containing bundled tools.

A report-generation Skill was created with a bundled `Export Excel V2` tool.

The intended architecture is:

```text
Execution & Reporting Agent
        ↓
Bid Analysis Report Generator Skill
        ↓
Export Excel V2
```

The ability of a Skill to invoke its bundled tool successfully at runtime remains open until a controlled test is completed.

## Evidence boundary

The screenshots and UI observations establish the existence of the controls and their visible configuration choices. They do not by themselves prove:

- Handoff serialization.
- Same-Agent re-entry semantics.
- Skill invocation behavior.
- Export artifact persistence.
- Script state-update transactionality.
- Nested object expression behavior in every context.
- Final Output artifact delivery.
