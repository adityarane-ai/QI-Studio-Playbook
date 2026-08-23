# Approval, Condition, Compute Verification Queue

**Status:** Active

This queue contains only unresolved questions. Once runtime evidence establishes an item, move the conclusion into `04-Evidence/` and the corresponding canonical reference, then remove the resolved question from this file.

## Approval

- What exact output variable or state structure records the human decision?
- Is the decision automatically available in system state, flow state, node output, or more than one location?
- Does the node resume reliably after the human action using the same checkpoint/session?
- What happens if the approval request receives no decision?
- Can downstream nodes consume the approval result directly without an explicit write-to-state step?

## Condition

- What expression syntax is accepted by the Condition node?
- Which operators are supported?
- How are missing, empty, and null values handled?
- Are string and numeric comparisons strongly typed or coerced?
- How is a no-match condition represented and routed?
- What happens when a condition references a variable that was never created?

## Compute

- What expression syntax/functions are supported?
- How are state references written inside expressions?
- How is the result written to state?
- Can one Compute node produce multiple keys?
- What happens on invalid expressions or incompatible types?
- Does `Produces keys` control completion/order for Compute in the same way as other state-producing steps?

## Composition

- Can Compute output be consumed immediately by Condition in the same run?
- Does Approval output behave like ordinary state for Condition/Compute consumption?
- Do resumed human-interaction runs preserve all previously produced flow variables?
