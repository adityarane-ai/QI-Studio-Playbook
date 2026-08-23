# QI Studio Verification Queue

**Purpose:** Active questions only. When answered by a reproducible runtime test or stronger authoritative evidence, move the result into the relevant evidence/canonical document and remove the item here.

## Output and variables

- **OUT-001:** Complete Output expression coverage beyond `{{flow.startTestResponse}}` and `{{system.humanInput}}`.
- **VAR-001:** Complete Flow Variable lifecycle: creation, overwrite, collection/object mutation, and behavior across other producers.
- **VAR-002:** Whether custom System-scope variables behave like built-in System variables.

## Human Input

- **HI-001:** Exact `input` output-variable serialization across response modes.
- **HI-002:** Ordering between response persistence and Advanced → State Update.
- **HI-003:** Timeout, abandonment and resume behavior.

## Approval

- **AP-001:** Exact runtime value/casing of `decision` after Approve and Reject.
- **AP-002:** Whether custom button labels affect serialized decision values.
- **AP-003:** State Update ordering and transactionality relative to route selection.
- **AP-004:** Pause/resume/timeout behavior.
- **AP-005:** Approver identity, timestamps, comments and audit metadata.
- **AP-006:** Runtime behavior when one decision branch is unconnected.

## Rule

- **RULE-001:** Null vs missing vs empty-string behavior.
- **RULE-002:** Case sensitivity and numeric/string coercion.
- **RULE-003:** Dates and collection behavior for `Contains`.
- **RULE-004:** Nested AND/OR grouping and precedence.
- **RULE-005:** Behavior when a referenced field is missing.

## Decision Tree

- **DT-001:** Produces-key completion and loop semantics.
- **DT-002:** Internal condition type coercion and missing-key behavior.
- **DT-003:** Ask User result persistence for typed replies, widgets and person-handled responses.
- **DT-004:** Tool Call result paths, retries, timeouts and error routing.
- **DT-005:** Compute and Done state mutation/clearing order and final-message behavior.
- **DT-006:** Resume behavior when interrupted mid-tree.

## Script

- **SCRIPT-001:** JavaScript runtime version, globals/modules, async behavior, time/resource limits.
- **SCRIPT-002:** Input/output schema enforcement for missing, extra and wrong-type values.
- **SCRIPT-003:** State Update ordering and transactionality.
- **SCRIPT-004:** Exact error handling and retry/fallback behavior.

## Agent

- **AG-001:** Context Management threshold accounting and message replacement/drop semantics.
- **AG-002:** Long-term Memory persistence and retrieval timing.
- **AG-003:** Include Thoughts runtime representation and downstream exposure.
- **AG-004:** Error Handling and recovery semantics.
- **AG-005:** Deep Agent subagent scheduling, aggregation and partial failure for 0–3 subagents.
- **AG-006:** Agent State Update semantics and variable-scope precedence.
- **AG-007:** Exact structured output mapping downstream.

## Agent tools and artifacts

- **TOOL-001:** End-to-end execution and artifact persistence for Export Excel V2, PowerPoint V2, PDF V2, Word V2 and HTML V2.
- **TOOL-002:** Extract Document to Markdown fidelity, OCR/vision quality, BlobProxy output and malformed-file behavior.
- **TOOL-003:** ConversationAttachment and ExportBlob retrieval and final attachment delivery, including multiple files.
- **TOOL-004:** Web Search runtime result shape and citation propagation.
- **TOOL-005:** Store/Retrieve memory-bag persistence scope and lifecycle.
- **TOOL-006:** Send Email recipient validation, attachments and error/result behavior.
- **TOOL-007:** SearchSystemTools → GetSystemToolSchema → ExecuteSystemTool runtime and failure behavior.
- **TOOL-008:** Knowledge workflow prerequisite enforcement for `get-knowledge-workflow-instructions`.

## Additional node families

LLM, External Agent, Compute, Subflow, Handoff, Guardrail and Output have capability-level observations but incomplete dedicated configuration/runtime evidence. Do not infer detailed contracts until dedicated captures/tests exist.

## Promotion rule

For every confirmed item:

1. Record the controlled test and result in `04-Evidence`.
2. Update the relevant page in `03-Canonical-Reference`.
3. Remove the item from this queue.
4. Keep failed or historical tests in evidence/history.
