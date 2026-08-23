# Additional Observed QI Studio Nodes

> **Evidence status:** OBSERVED / DOCUMENTATION-BOUND
> **Evidence date:** 2026-08-23

This page captures additional node families established in the broader QI Studio screenshot/documentation work but not yet documented with the same depth as the core primitive pages.

## LLM

Use an LLM node for a direct language-generation or transformation step when autonomous tool planning and multi-step reasoning are unnecessary.

Prefer an LLM over an Agent for one well-defined prompt-to-output task.

Open questions: exact model/provider options, structured output constraints, context limits, and error semantics should be verified per build.

## External Agent

Use an External Agent boundary when responsibility is delegated outside the current orchestration context.

Document:

- input contract
- output contract
- authentication boundary
- timeout behavior
- error propagation
- ownership of the returned result

Do not treat an external agent as an implicit substitute for workflow control.

## Compute

Compute is the deterministic calculation/transformation primitive.

Use it for:

- arithmetic
- normalization
- derived values
- explicit state transformations

Do not use an LLM or Agent for exact arithmetic when deterministic computation is available.

## Subflow

Subflow encapsulates a reusable workflow segment behind a process boundary.

Use it when the child process has a coherent responsibility and should be reused, isolated, or governed separately.

Avoid hiding an otherwise unclear architecture inside nested subflows.

## Handoff

Handoff transfers responsibility between workflow participants.

The transfer should preserve the context needed by the receiver and explicitly define what the receiver owns next.

Typical questions:

- What is being handed off?
- To whom?
- What state is authoritative?
- What response is expected?
- What happens on failure?

## Guardrail

Guardrails enforce safety or policy constraints around agentic execution.

Use structural controls for critical constraints rather than relying only on natural-language instructions.

Open questions include exact sequencing, failure behavior, and whether the current build supports pre-action and post-action guardrails symmetrically.

## Human Input

Human Input is appropriate when the workflow needs a person to provide information rather than merely approve or reject an action.

Use explicit validation and state capture so that downstream nodes can consume the response deterministically.

For binary authorization, prefer Approval.

## Output

Output is the workflow emission boundary.

Define the output contract explicitly when the result will be consumed by another system or workflow.

Avoid using free-form text as the primary interface when downstream consumers need structured data.

## Coverage status

These nodes are recognized as part of the broader QI Studio capability set from prior screenshot/documentation work. They require deeper node-specific UI evidence and runtime tests before their behavior should be considered fully verified.
