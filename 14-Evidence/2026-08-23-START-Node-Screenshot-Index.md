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
9. START-node reference documentation: trigger, inputs, state and state-update behavior.

## Companion pages

- [START Node](../02-Orchestration-Primitives/Start.md)
- [Runtime API](../04-Tools/API-Reference-and-Runtime-API.md)
- [Variable Foundations](../05-Data-State/Variables-Foundations.md)
- [START Test Matrix](../11-Testing/START-Node-Test-Matrix.md)
- [START evidence record](2026-08-23-START-Node.md)
- [START capability snapshot](START-2026-08-23-Capability-Snapshot.md)

## Visual evidence handling

The raw screenshots are the primary visual evidence supplied in the conversation. This repository currently stores the normalized interpretation and evidence metadata. The original binary images should be archived under `14-Evidence/Screenshots/<evidence-id>/` when binary archival through the GitHub workflow is enabled.

Do not treat the presence of an image as proof of runtime semantics that were not tested.

## Evidence discipline

Screenshots establish UI/configuration facts. They do not by themselves establish exact retry, transaction, deduplication, null, persistence, or reconnect semantics. Those questions remain explicitly tracked in the test matrix.
