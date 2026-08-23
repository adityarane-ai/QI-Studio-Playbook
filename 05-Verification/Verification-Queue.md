# Verification Queue

This is the project-level list of unresolved questions. Detailed tests live in `07-Test-Lab/`; executed results live in `04-Evidence/`.

## Rules

- Include only active unresolved questions.
- Give each item a stable ID.
- Link to the canonical capability and planned test.
- Remove an item when evidence resolves it.

## Initial priority queue

### P0: Core orchestration contracts

- Output response-source resolution beyond the two tested references.
- Human Input timeout, abandonment, resume, and non-free-text modes.
- Approval routing, decision serialization, and resume behavior.
- Variable scope precedence and lifecycle beyond the tested Human Input cases.

### P1: Agent runtime semantics

- Context Management behavior.
- Long-term Memory persistence and retrieval timing.
- State Update semantics.
- Error Handling and recovery.
- Deep Agent subagent execution and aggregation.

### P1: Tool runtime semantics

- Export artifact generation and `system.files` behavior.
- Attachment delivery from generated or existing files.
- Web Search result/citation propagation.
- Store/Retrieve persistence scope.
- ConversationAttachment and multi-file behavior.
- Knowledge-workflow prerequisite enforcement.

### P2: Deterministic edge cases

- Rule type coercion, missing values, and Boolean grouping.
- Decision Tree state/condition edge cases.
- Script runtime limits and schema enforcement.
- Variable Update transactionality and conditional behavior.

## Promotion

When resolved, record the result in `04-Evidence/`, update the relevant `03-Canonical-Reference/` page, and remove the item here.
