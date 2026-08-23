# Evidence Record: START Node and Runtime API

**Evidence ID:** EVID-START-001  
**Date:** 2026-08-23  
**Capability:** START node, input schema, state updates, runtime API, HILT resume, SSE stream  
**Status:** CONFIRMED for UI/documentation observations.

## Source set

A screenshot batch supplied by the user included:

- API Reference: Invoke Agent
- API Reference: Authentication token configuration
- API Reference: HILT resume
- HILT decision payload shape
- SSE event types
- START trigger configuration
- START inputs and advanced state updates
- String input variable editor
- Object input variable editor
- START node product documentation

## Confirmed observations

### START

- Exactly one START node exists per orchestration.
- START cannot be deleted.
- API invocation is always available.
- Scheduler and Event are optional trigger mechanisms.
- The documentation screenshot labels automatic triggers as experimental.
- Inputs can be configured with name, type, description, required/optional status, validation rules, enum values and defaults.
- Standard examples include `message`, `attachments`, and `ui_action`.
- START supports state updates.

### State

Observed operations include Set, Append and Clear. Individual updates can have `Run only when` conditions.

Observed mappings include:

```text
message -> system/userQuery
attachments -> system/attachments
ui_action -> system/uiAction
options.sessionId -> system/sessionId
```

The example also appends the user message into `conversationHistory`.

### Runtime API

- Runtime execution uses bearer authentication.
- Design-time and runtime tokens are exposed separately in the UI.
- `sessionId` supports conversation continuity and may be auto-generated.
- `includeIntermediateParts` is an invoke option.
- HILT interruption data can be resumed through a second invoke request.
- SSE exposes run, step, text and tool lifecycle events.

## Not established by screenshots

The screenshots do not by themselves establish:

- exact runtime transactionality of state updates;
- state update ordering guarantees;
- retry semantics;
- resume failure behavior;
- stream reconnect semantics;
- scheduler/event retry behavior;
- complete API validation error contracts.

These remain open testing items.

## Related pages

- [START Node](../../02-Orchestration-Primitives/START.md)
- [START State Updates](../../05-Data-State/START-State-Updates.md)
- [API Integration](../../04-Tools/API-Integration.md)
- [Evidence Index](../README.md)
