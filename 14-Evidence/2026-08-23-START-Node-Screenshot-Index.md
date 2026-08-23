# START Node Screenshot Evidence Index

**Evidence ID:** QI-OBS-2026-08-23-START-SCREEN-001

This evidence set contains the screenshots supplied together for START-node analysis and the adjacent API/runtime behavior.

## Included evidence themes

1. API Reference: invoke endpoint, request structure, Authorization header, sessionId, intermediate parts, resume object.
2. Auth Token Configuration: distinction between design-time and runtime token fields.
3. HITL Resume: interrupt payload, tool approval, decisions array, resume metadata.
4. SSE Stream: runtime event taxonomy and streaming request example.
5. START trigger configuration: API invocation, Scheduler, Event, connector selection.
6. START inputs: message, attachments, ui_action.
7. START state updates: conversationHistory, system/files, system/userQuery, system/attachments, system/uiAction, system/sessionId.
8. Variable editor: scalar validation and object/JSON Schema configuration.
9. START-node reference documentation: trigger, inputs, state/memory behavior.

## Companion page

Read [START Node](../02-Orchestration-Primitives/Start.md) for normalized behavior and design guidance. Read [Runtime API](../04-Tools/API-Reference-and-Runtime-API.md) for the API reference findings.

## Visual evidence

A contact sheet of the supplied screenshots is stored alongside this evidence record. The textual pages remain the authoritative interpretation layer because screenshots alone do not establish runtime semantics that were not directly tested.

## Evidence discipline

The screenshots establish UI/configuration facts. They do not, by themselves, establish exact retry, transaction, deduplication, null, persistence, or reconnect semantics. Those are explicitly tracked as experiments/open questions.
