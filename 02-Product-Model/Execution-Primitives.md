# QI Studio Execution Primitives

**Status:** Current conceptual model based on observed UI, supplied product guidance, and the current platform implementation work. Detailed runtime semantics remain open unless separately runtime-confirmed.

## 1. Purpose

QI Studio is best understood as a composition environment for different execution primitives rather than as a collection of interchangeable nodes.

The central design question is not simply **which node is available?** It is **what kind of responsibility must this step own?**

Use the primitive whose execution semantics match the responsibility:

```text
Semantic judgement       → Agent / LLM
Explicit deterministic   → Rule / Decision Tree / Script / Compute
State persistence        → Variable Update / State Update
Human information       → Human Input
Human authorization     → Approval
Delegation               → Handoff / Subflow / External Agent
Policy enforcement       → Guardrail
Final emission           → Output
External capability      → Tool / Connector / integration
```

The strongest QI Studio architectures combine these primitives instead of asking one primitive to do everything.

## 2. Responsibility Model

### Semantic primitives

Semantic primitives are appropriate when the workflow must interpret, synthesize, compare, classify, reason, or generate.

#### Agent

The Agent is the primary semantic execution boundary. Its observed surface includes model/strategy selection, messages, tools and capabilities, context, memory, state updates, output formats, guardrails and error handling.

Use Agent when the difficult part of the task is judgement or interpretation.

Do not use Agent as a replacement for exact arithmetic or explicit business rules.

#### LLM

LLM belongs to the semantic family but should be treated separately from Agent until its complete QI Studio runtime contract is established.

Use the LLM primitive only when its specific interaction model is required and documented.

## 3. Deterministic Primitives

Deterministic primitives should own rules whose result should be reproducible from explicit inputs.

### Rule

Use Rule for straightforward branching or condition evaluation.

Typical pattern:

```text
input/state
   ↓
Rule
   ├── true  → path A
   └── false → path B
```

Rule should be preferred over Agent when the condition is explicit and deterministic.

### Decision Tree

Use Decision Tree for structured multi-step deterministic routing where multiple branches, user interaction, tool calls, or internal state transitions form a defined decision process.

Decision Tree should not be treated as an opaque semantic substitute for Agent reasoning.

Its detailed loop, ask-user, compute, tool-call and resume behavior remains a runtime-verification area.

### Script

Script is a deterministic JavaScript execution boundary.

Use Script for:

- transformations
- calculations
- normalization
- reshaping
- object construction
- deterministic logic that is cumbersome in native branching nodes

The Script contract separates:

```text
Declared Inputs
      ↓
JavaScript execution
      ↓
Returned result
      ↓
Optional State Update
      ↓
Declared Outputs
```

The returned `result` and persistent State Update are different concepts and should not be conflated.

### Compute

Use Compute when an operation belongs to the platform's deterministic computation surface and does not require custom Script logic.

Do not use Agent reasoning for arithmetic or mechanical transformation that a deterministic primitive can own.

### Variable Update

Use Variable Update when the primary responsibility is explicit workflow-state mutation rather than calculation or semantic judgement.

This primitive should make state transitions visible in the graph instead of hiding them inside prompts.

## 4. Human Governance Primitives

### Human Input

Human Input is the free-form interaction primitive.

It pauses the workflow, asks a question, captures the response, and stores it in a configured target such as a Flow Variable or `system.humanInput`.

Use it when the workflow needs information, clarification, correction, or other free-form human input.

Do not use Human Input as a substitute for a formal approval gate unless the workflow explicitly intends to interpret free text as an approval decision.

### Approval

Approval is the governance primitive for explicit authorization and Approved/Rejected routing.

Use Approval when a person must authorize an action or decision rather than merely provide information.

Human Input and Approval are intentionally separate concepts:

| Requirement | Human Input | Approval |
|---|---|---|
| Free-form information | Yes | No, primary purpose is not free-form collection |
| Binary authorization | Not the intended primitive | Yes |
| Explicit Approved/Rejected routing | Not established as the primary model | Yes |

## 5. Delegation and Composition

### Handoff

Handoff transfers responsibility from one Agent to another Agent or downstream specialist according to an explicit delegation contract.

A robust Handoff pattern is:

```text
Calling Agent
    ↓
Handoff Tool
    ├── task
    ├── relevant inputs/state
    ├── constraints
    └── expected output contract
    ↓
Target Agent
    ↓
Return context
    ↓
Calling Agent
```

Typed handoff parameters are preferable to relying only on graph-edge context because they make the delegation contract explicit and reviewable.

The exact runtime serialization and aggregation behavior of Handoff remains an open verification area until tested.

### Subflow

Subflow should be understood as workflow composition. It is useful when an independently reusable sequence of nodes should be encapsulated behind a bounded interface.

Do not infer detailed subflow execution semantics until dedicated evidence exists.

### External Agent

External Agent belongs to the delegation/composition family and is appropriate when semantic work is intentionally delegated outside the local Agent execution boundary.

Its detailed runtime contract remains an evidence/verification topic.

## 6. Safety and Policy

### Guardrail

Guardrail is the policy/safety control surface.

Use it to impose constraints around what an execution path may do.

Guardrail should be treated as a cross-cutting governance mechanism rather than as a replacement for deterministic validation or semantic evaluation.

Its precise insertion points and runtime behavior require dedicated verification.

## 7. Output Primitive

### Output

Output is the user-facing completion boundary.

A workflow can execute successfully upstream and still fail to produce a useful user-visible response if Output has no valid response source.

The Output node therefore has an explicit contract:

```text
Upstream result/state
       ↓
Explicit response source
       ↓
Output
       ↓
User-visible completion
```

The tested platform behavior confirms explicit response references such as:

```text
{{flow.startTestResponse}}
{{system.humanInput}}
```

Broader expression coverage remains under verification.

## 8. Tools and Integrations Are a Separate Layer

A tool is an executable capability exposed to an Agent or Skill. It should not be confused with the Agent itself.

The platform therefore has a layered model:

```text
Workflow Node
   ↓
Agent / Skill
   ↓
Capability
   ↓
Tool / Connector / Widget / Library
   ↓
External action or artifact
```

The exact capability surface varies by feature and must be documented separately from node taxonomy.

## 9. Core Design Principle

The best QI Studio workflows use a **hybrid execution architecture**:

```text
Interpretation
   ↓
Agent
   ↓
Deterministic normalization
   ↓
Script / Compute
   ↓
Explicit routing
   ↓
Rule / Decision Tree
   ↓
Human governance where required
   ↓
Human Input / Approval
   ↓
Delegation where responsibility is specialized
   ↓
Handoff / Subflow
   ↓
External action / artifact
   ↓
Tool
   ↓
Output
```

The goal is not to minimize node count. The goal is to place each responsibility in the primitive that provides the clearest semantics, strongest control, and easiest verification.

## 10. Semantic vs Deterministic Boundary

A useful architectural boundary is:

```text
Semantic layer
    Agent / LLM
    ↓
    interpretation / judgement / synthesis

Deterministic layer
    Rule / Decision Tree / Script / Compute
    ↓
    exact logic / calculation / transformation

Governance layer
    Human Input / Approval / Guardrail
    ↓
    human or policy control

Integration layer
    Handoff / Subflow / tools / connectors
    ↓
    delegation / external capability
```

Do not hide deterministic business rules inside Agent prompts when a native deterministic primitive can express them.

Do not force deterministic nodes to perform semantic judgement they cannot reliably express.

## 11. Reusable Architecture Patterns

### Semantic → deterministic

```text
Agent
  ↓
Script / Rule
```

Use semantic reasoning to interpret messy input, then use deterministic logic to normalize or route it.

### Agent → specialist Agent

```text
Agent
  ↓
Handoff
  ↓
Specialist Agent
  ↓
Return
```

Use explicit task, input, constraint, and expected-output contracts.

### Agent → Skill → Tool

```text
Agent
  ↓
Skill
  ↓
Tool
```

Use a Skill to encapsulate reusable domain capability and attach the tools required to execute that capability.

### Human clarification

```text
Agent
  ↓
Human Input
  ↓
State
  ↓
Validation
```

Use this when semantic discovery requires a person to confirm or correct the interpretation before downstream execution.

### Deterministic pipeline

```text
Script
  ↓
Script
  ↓
Script
  ↓
Result
```

Prefer this for exact transformations, calculations, and validation chains.

## 12. What Is Established vs Open

Observed or documented capabilities should be distinguished from runtime-confirmed semantics.

Current runtime-confirmed examples include the tested Human Input → variable → Output path.

The following remain broader verification topics:

- exact Handoff serialization and return aggregation
- Decision Tree execution and resume semantics
- Script runtime limits and advanced state-update behavior
- Agent context and memory semantics
- Skill invocation and bundled-tool execution
- export artifact persistence
- broader Output expression coverage
- Subflow and External Agent runtime behavior

The Playbook should not convert the existence of a UI control into a claim about full runtime behavior without evidence.

## 13. Selection Heuristic

When choosing a primitive, ask in this order:

1. Is the requirement exact and deterministic?
   - Use Rule, Compute, Script, Variable Update, or Decision Tree.

2. Does it require semantic interpretation or judgement?
   - Use Agent or the appropriate semantic primitive.

3. Does a person need to provide information?
   - Use Human Input.

4. Does a person need to authorize an action?
   - Use Approval.

5. Does another specialist need to own the task?
   - Use Handoff, Subflow, or External Agent as appropriate.

6. Is a policy/safety constraint being enforced?
   - Use Guardrail.

7. Is an external capability or artifact operation required?
   - Use a Tool, Connector, or integration.

8. Is the workflow finished and a user-facing response required?
   - Use Output.

This decision model is more durable than memorizing individual node names because it is based on responsibility.