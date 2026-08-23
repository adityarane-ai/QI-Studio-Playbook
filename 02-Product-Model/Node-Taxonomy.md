# Node Taxonomy

**Status:** Current structural model; detailed behavior belongs in `03-Canonical-Reference` and evidence in `04-Evidence`.

QI Studio nodes are classified by responsibility rather than discovery order.

| Category | Nodes / families | Role |
|---|---|---|
| Entry and interaction | Start, Human Input, Approval | Enter workflow, collect human information, or obtain human authorization |
| Deterministic control flow | Rule, Decision Tree, Condition | Explicit branching and state-driven routing |
| State and transformation | Variable Update, Compute, Script | Persist or transform explicit workflow data |
| Semantic reasoning | Agent, LLM | Interpretation, synthesis, judgement, generation |
| Human governance | Approval, Handoff | Human checkpoint or responsibility transfer |
| Composition and delegation | Subflow, External Agent | Encapsulate or delegate workflow responsibility |
| Safety | Guardrail | Enforce policy/safety constraints around execution |
| Output and integration | Output and tool-backed actions | Emit workflow results or interact with external capabilities |

## Coverage status

Core documented/reference depth exists for Start, Human Input, Approval, Decision Tree, Rule, Script, Variable Update and Agent. Compute and Condition have existing canonical references. Additional families such as LLM, External Agent, Subflow, Handoff, Guardrail and Output are recognized from supplied evidence but require deeper node-specific captures and runtime tests before detailed behavior is treated as verified.

## Ownership rule

- This file defines the conceptual taxonomy.
- `03-Canonical-Reference/Nodes/` explains current node behavior.
- `04-Evidence/` stores supporting captures and runtime results.
- `05-Verification/` contains only unresolved questions.
