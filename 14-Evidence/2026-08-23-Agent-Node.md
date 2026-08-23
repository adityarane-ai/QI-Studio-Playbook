# Evidence Record: Agent Node

**Evidence ID:** QI-OBS-2026-08-23-AGENT-001  
**Date:** 2026-08-23  
**Capability:** Agent strategy, model selection, prompt/message roles, tools/capabilities, advanced settings, context management, long-term memory, state updates, variable browser, outputs.  
**Source:** User-supplied QI Studio screenshots.

## What was observed

1. Agent strategy selection includes ReAct and Deep Agent in the observed UI.
2. Deep Agent exposes Enable SubAgents, a displayed maximum of three parallel subagents, and a parallel execution toggle.
3. Agent model selection exposes multiple provider families, including OpenAI, Google, and Anthropic.
4. The Agent message editor supports system, user, and assistant roles and variable insertion.
5. The Agent exposes Tools, Libraries, Skills, Widgets, and Connectors sections.
6. Advanced settings expose Response Format, Include Thoughts, Guardrails, Context Management, Long-term Memory, Error Handling, State Update, and Output Variables.
7. Response Format visibly offers Plain Text and JSON.
8. Context Management exposes Tool results only and Full history scopes.
9. Context Management exposes Replace and Drop strategies.
10. Context Management threshold can be configured using Tokens or Turns.
11. Long-term Memory is a separate toggle and is described as persisting useful facts across sessions.
12. State Update exposes Append, Extend, Set, and Clear in the observed Agent UI.
13. Conversation-history state updates expose User, Assistant, System, and Tool roles.
14. The variable browser groups values by scopes including Current Node, workflow/referenced contexts, Environment, Authorization, Metadata, Flow, and System.
15. The variable browser visibly includes system values such as attachments, files, humanInput, sessionId, timestamp, uiAction, and userQuery.
16. The variable browser visibly includes environment/authentication/metadata values such as baseURL, Authorization, RuntimeToken, agentName, modelId, version, and workflowId.
17. Flow/business objects visible in the browser include evaluationCriteria, knockoutResult, scoringResult, supplierSubmission, and validationResult.
18. Agent output variables visibly include text, toolCalls, structuredOutput, success, error, error.message, and error.status_code.

## What this evidence proves

It proves that these configuration controls, scopes, values, and output fields are present in the observed QI Studio build/UI.

## What this evidence does not prove

Screenshots alone do not establish exact runtime semantics for context replacement, dropping, thresholds, long-term-memory retrieval timing, error retries, guardrail sequencing, Include Thoughts payloads, transactionality of state updates, scoped-variable precedence, or Deep Agent scheduling/failure semantics.

## Security note

The screenshots show fields named Authorization and RuntimeToken in the variable browser. These are system-owned sensitive values. Real credentials must never be stored in the playbook.

## Follow-up experiments

- Compare Tool-results-only vs Full-history context management under identical prompts and large contexts.
- Compare Replace vs Drop behavior and inspect actual message payloads/tokens.
- Test threshold boundary conditions in Tokens and Turns modes.
- Enable/disable Long-term Memory and verify persistence across sessions.
- Test Agent state updates with Append, Extend, Set, Clear on arrays and objects.
- Trigger a failing Agent and document Error Handling behavior.
- Test structuredOutput vs text downstream consumption.
- Test Deep Agent with 0, 1, 2, and 3 subagents and observe scheduling/aggregation.
- Test partial subagent failure and parent behavior.
- Test variable resolution when same-named fields exist in multiple scopes.
- Determine exact runtime representation and exposure behavior for Include Thoughts.

## Related

- [Agent Node](../02-Orchestration-Primitives/Agent.md)
- [Testing](../11-Testing/README.md)
- [Tools](../04-Tools/README.md)
- [Data and State](../05-Data-State/README.md)
