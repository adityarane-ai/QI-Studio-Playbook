# Approval Node Evidence Record

**Evidence ID:** QI-OBS-2026-08-23-APPROVAL-001  
**Date:** 2026-08-23  
**Capability:** Human approval checkpoint, configurable approval message, action labels, Approved/Rejected routing, state updates, and decision output variable.  
**Source:** User-supplied QI Studio Approval screenshot and accompanying product guidance text.

## Direct observations

- Canvas identifies an Approval node.
- Approval Message is configurable and the shown example is `Approval required to proceed.`
- Approve Button and Reject Button are configurable; defaults shown are `Approve` and `Reject`.
- Two outgoing handles are visible: `Approved` and `Rejected`.
- Advanced exposes State Update.
- Advanced exposes Output Variables.
- A `decision : string` output variable is visible with description that the user's decision is `approve` or `reject`.

## Product-guidance semantics captured

The node pauses the orchestration, waits for a person to approve or reject, presents the approval message, provides two action buttons, and routes according to the selected action. It is intended for sensitive or irreversible operations. Button labels can be customized.

## Evidence boundary

The capture does not establish exact runtime serialization of `decision`, effect of custom button labels on serialized values, timeout/resume behavior, approver identity or audit metadata, state-update transactionality, unconnected branch behavior, or replay/idempotency semantics.

## Next verification tests

| ID | Test | Purpose |
|---|---|---|
| APPROVAL-001 | Approve path | Confirm Approved route |
| APPROVAL-002 | Reject path | Confirm Rejected route |
| APPROVAL-003 | Decision after Approve | Confirm exact serialized value |
| APPROVAL-004 | Decision after Reject | Confirm exact serialized value |
| APPROVAL-005 | Custom labels | Determine effect on decision output |
| APPROVAL-006 | State Update | Confirm timing/persistence |
| APPROVAL-007 | Unconnected branch | Determine runtime behavior |
| APPROVAL-008 | Long pause | Determine timeout/resume |
| APPROVAL-009 | Repeated execution | Determine replay semantics |
| APPROVAL-010 | Audit metadata | Determine identity/timestamp exposure |

Do not store credentials, runtime tokens, API keys, or sensitive business payloads in evidence.
