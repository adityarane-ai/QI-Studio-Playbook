# Evidence Record: START Node

**Evidence ID:** QI-OBS-2026-08-23-START-001  
**Date:** 2026-08-23  
**Capability:** START node, input variables, state updates, trigger configuration  
**Source:** User-supplied QI Studio screenshots and QI Studio START-node documentation visible in those screenshots.

## What was observed

1. START is the single entry node and cannot be deleted.
2. API invocation is always available.
3. Scheduler and Event appear as optional automatic trigger choices.
4. The UI exposes `message`, `attachments`, and `ui_action` as entry inputs in the observed workflow.
5. Input variables have editable type, description, requiredness, validation rules, allowed values, and defaults.
6. Object inputs support a visual editor and JSON Schema.
7. START Advanced settings expose State Update.
8. State Update supports operations including Set, Append, and Clear.
9. State Update supports Run only when conditions.
10. The observed configuration updates conversation history and system context fields including files, user query, attachments, UI action, and session ID.

## What this evidence proves

It proves that these controls and configuration concepts exist in the observed QI Studio build/UI.

## What it does not prove

It does not, by screenshot alone, prove exact runtime semantics for retries, event deduplication, atomicity, null handling, or cross-run state persistence.

## Follow-up experiments

- Remove optional input vs send explicit null.
- Trigger the same Event twice and inspect duplication behavior.
- Fail one of multiple START state updates and inspect transaction semantics.
- Invoke with explicit `sessionId`, then omit it, then resume an interrupted run.
- Test validation rules at runtime for strings and objects.
- Test Scheduler retry and misconfiguration behavior.

## Related

- [START node](../02-Orchestration-Primitives/Start.md)
- [Variables and State](../05-Data-State/README.md)
- [API Reference](../04-Tools/API-Reference-and-Runtime-API.md)
- [Testing](../11-Testing/README.md)
