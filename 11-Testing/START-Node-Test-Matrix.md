# START Node Runtime Test Matrix

These tests convert UI observations into runtime knowledge. Do not mark a result TESTED until the experiment is actually executed.

| ID | Question | Setup | Expected | Status |
|---|---|---|---|---|
| START-001 | Missing required `message` | Invoke without message | Request rejected with clear validation error | OPEN |
| START-002 | Optional `attachments` omitted | Invoke without attachments | Flow runs with documented empty/absent behavior | OPEN |
| START-003 | Optional `attachments` explicitly null | Send null | Distinguish from omission | OPEN |
| START-004 | Regex validation | Invalid value | Validation blocks invalid input | OPEN |
| START-005 | Enum validation | Unsupported value | Validation blocks unsupported value | OPEN |
| START-006 | Object schema | Invalid object shape | Runtime enforcement matches configuration | OPEN |
| START-007 | Set state update | Write new value | Target is replaced | OPEN |
| START-008 | Append state update | Add to existing history | Existing collection retained and new entry appended | OPEN |
| START-009 | Clear state update | Preload stale value | Target is cleared before downstream use | OPEN |
| START-010 | Conditional state update | Condition false | Update is skipped | OPEN |
| START-011 | Session continuity | Invoke twice with same sessionId | Conversation/runtime continuity is preserved | OPEN |
| START-012 | Auto sessionId | Invoke without sessionId | Runtime generates session continuity identifier | OPEN |
| START-013 | Event duplicate | Trigger same event twice | Determine deduplication/duplicate execution semantics | OPEN |
| START-014 | Scheduler failure | Force job failure | Determine retry/backoff behavior | OPEN |
| START-015 | Partial state-update failure | Cause one update to fail | Determine transaction/partial-commit semantics | OPEN |
| START-016 | HITL resume | Pause and resume using interrupt metadata | Execution continues from interrupt checkpoint | OPEN |
| START-017 | SSE ordering | Stream run with tools | Determine observed event ordering guarantees | OPEN |
| START-018 | Intermediate parts | Enable `includeIntermediateParts` | Determine exactly what metadata/parts are emitted | OPEN |

## Test hygiene

For every executed test, add an evidence record with:

- experiment ID
- QI build/version if known
- exact configuration
- input payload
- expected result
- actual result
- runtime events/error
- workaround if any
- confidence
- screenshot/log references
