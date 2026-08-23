# Anti-Patterns

These are patterns to avoid unless there is a documented, tested reason.

## Giant Master Agent

**Problem:** one agent owns planning, state, routing, approvals, validation, execution, and synthesis.

**Better:** workflow controls state and routing; agents own bounded reasoning.

## Over-agentification

**Problem:** using agents for rules, arithmetic, simple branching, or deterministic lookups.

**Better:** Rule, Decision, Compute, or Tool.

## Agent-owned workflow state

**Problem:** the LLM decides what the workflow stage is and mutates authoritative state implicitly.

**Better:** explicit workflow variables and transitions.

## Unlimited tool access

**Problem:** huge tool surface increases selection errors and unintended actions.

**Better:** task-specific tool bundles and controlled discovery.

## Unbounded reflection

**Problem:** agent-review-agent loops without a clear stop condition.

**Better:** explicit challenge criteria and bounded retries.

## Hidden business rules

**Problem:** rules are embedded only in prompts and cannot be audited or tested.

**Better:** encode deterministic rules in workflow controls and preserve the rule contract.

## LLM-owned arithmetic

**Problem:** totals, weights, rankings, and deterministic calculations become non-reproducible.

**Better:** Compute or deterministic execution services.

## Invented identifiers

**Problem:** agents fabricate IDs, schema fields, or source references.

**Better:** obtain identifiers from authoritative tools and pass them explicitly.

## Knowledge as authority

**Problem:** contextual knowledge silently overrides source documents or human-confirmed configuration.

**Better:** maintain a strict evidence/authority boundary.
