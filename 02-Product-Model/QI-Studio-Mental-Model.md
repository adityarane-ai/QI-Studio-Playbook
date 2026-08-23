# QI Studio Mental Model

> **Status:** Platform model based on observed UI, supplied product guidance, and current design work. Runtime semantics remain unverified unless explicitly marked as Runtime Confirmed.
>
> **Scope:** Platform understanding, not a specific orchestration.

## 1. What QI Studio Is

QI Studio is best understood as a graph-based orchestration environment in which different execution primitives can be composed into a workflow.

The important distinction is that a QI Studio workflow is not simply a sequence of AI prompts. It is a runtime composed of:

- workflow nodes
- graph connections
- explicit workflow state
- semantic reasoning boundaries
- deterministic computation boundaries
- human interaction/governance points
- delegation and composition mechanisms
- agent capabilities
- tools and integrations
- output/artifact boundaries

This means platform design should begin with responsibility and execution semantics rather than with a preferred AI model.

## 2. Core Mental Model

```text
                         QI STUDIO WORKFLOW
                                |
        +-----------------------+-----------------------+
        |                       |                       |
     GRAPH                  STATE                  CAPABILITIES
        |                       |                       |
   Nodes + Edges       Flow / System / Results   Tools / Skills /
        |               Conversation / Runtime    Libraries /
        |               Memory                   Widgets / Connectors
        |                       |                       |
        +-----------------------+-----------------------+
                                |
                         EXECUTION RUNTIME
                                |
        +-----------------------+-----------------------+
        |                       |                       |
   Deterministic           Semantic                Human /
   execution               execution               governance
        |                       |                       |
 Rule / Script /        Agent / LLM             Human Input /
 Compute / Variable                           Approval / Handoff
 Update / Decision Tree
```

## 3. Execution Primitive Families

QI Studio nodes should be understood by the responsibility they perform.

### Entry and interaction

- Start
- Human Input
- Approval

These establish how a workflow begins or pauses for human interaction.

### Deterministic control

- Rule
- Decision Tree
- Condition

These express explicit branching and control logic.

### State and transformation

- Variable Update
- Compute
- Script

These manipulate explicit workflow data or perform deterministic computation.

### Semantic reasoning

- Agent
- LLM

These are appropriate where interpretation, synthesis, judgement, or generation is required.

### Delegation and composition

- Handoff
- Subflow
- External Agent

These transfer or encapsulate responsibility rather than performing the substantive reasoning themselves.

### Safety and governance

- Guardrail
- Approval

These constrain or authorize execution.

### Output and integration

- Output
- tool-backed actions

These cross the workflow boundary into a user-visible or external result.

## 4. The Most Important Platform Boundary

A useful design rule is:

```text
Semantic judgement -> Agent / LLM
Exact logic        -> Rule / Decision Tree / Script / Compute
Persistent state   -> Flow Variables / State Update
Human information  -> Human Input
Human authorization-> Approval
Delegation         -> Handoff / Subflow / External Agent
External capability-> Tool / Connector
Final response     -> Output
```

Do not hide deterministic business logic in prompts when a deterministic node can own it.

Do not use Script merely because code is available when a simple Rule can express the decision.

Do not use an Agent for arithmetic or exact state transitions that can be deterministic.

## 5. Graph Is Not the Same as State

The workflow graph defines possible execution paths.

State holds information that execution nodes consume and produce.

A useful conceptual distinction is:

```text
Graph = where execution can go
State = what execution knows / carries
Node result = what a node just produced
Conversation context = conversational material
Runtime context = execution metadata
Long-term memory = durable remembered information
```

These concepts should not be treated as interchangeable.

## 6. State Scopes

The platform exposes multiple kinds of values. Current model:

### Flow / workflow state

User-created variables used as explicit business/workflow state.

Examples:

```text
flow.runId
flow.currentStage
flow.criteria
flow.evaluationConfiguration
```

This should be the preferred location for important business state that must cross node boundaries.

### Built-in System values

Platform-managed values.

Observed examples include:

```text
system.humanInput
system.files
system.userQuery
system.attachments
```

System values are not equivalent to user-created Flow state.

### Node outputs

A node exposes its execution result separately from persistent workflow state.

For Script nodes, the documented pattern is:

```text
nodes.<nodeName>.result
```

and field-level references can be expressed as:

```text
{{nodes.<nodeName>.result.<field>}}
```

The distinction matters:

```text
return {...}
     -> node result

State Update
     -> workflow state mutation
```

### Conversation context / history

Conversational material used to provide context to semantic execution.

It should not be treated as the canonical storage location for structured business state.

### Runtime context

Execution metadata supplied by the platform.

It should not be used as a substitute for business state.

### Long-term memory

Durable information intended to persist beyond the immediate workflow/conversation context.

Its exact persistence/retrieval semantics remain an active verification area.

## 7. Agent Mental Model

An Agent is a semantic execution boundary, not merely a model invocation.

The Agent combines:

```text
Strategy
   +
Model
   +
System/User messages
   +
Tools / Libraries / Skills / Widgets / Connectors
   +
Context controls
   +
Memory controls
   +
State updates
   +
Output schema
   +
Error / guardrail controls
```

Observed strategies include `ReAct` and `Deep Agent`.

The Deep Agent surface includes subagent controls. The current UI observation exposes a visible maximum of three parallel subagents. Scheduling, aggregation and partial-failure behavior remain runtime-verification items.

## 8. Agent vs Tool vs Skill

These should be treated as different layers.

### Agent

Owns semantic reasoning and orchestration of a semantic task.

### Tool

Performs a concrete capability/action with a defined parameter contract.

Examples:

- Excel export
- document extraction
- web search
- email

### Skill

A reusable capability/instruction package that shapes how an Agent performs a class of work. A Skill may expose or bundle tools.

A useful pattern observed during platform design is:

```text
Agent
  |
  +-- Skill
        |
        +-- Tool
```

This allows domain-specific expertise to be separated from the Agent's core orchestration prompt.

## 9. Handoff Mental Model

Handoff is a delegation boundary.

The caller should provide a typed task contract rather than relying entirely on graph context.

Conceptually:

```text
Caller Agent
    |
    v
Handoff Tool
    |
    +-- task
    +-- relevant state/context
    +-- constraints
    +-- expected output contract
    |
    v
Target Agent / node
    |
    v
Return context
    |
    v
Caller Agent
```

The current implementation baseline uses explicit Handoff parameters and Deep Agent return edges configured with context passing and appended last-message transfer. Exact runtime serialization and aggregation remain open.

## 10. Human Interaction Model

Human Input and Approval serve different purposes.

### Human Input

Collect information or a free-form response.

Runtime-confirmed example:

```text
Human Input
   -> system.humanInput
   -> Output {{system.humanInput}}
```

### Approval

Represents an authorization gate with decision routing.

Do not use Human Input as a substitute for Approval when explicit authorization semantics are required.

## 11. Deterministic and Semantic Hybrid Architecture

The strongest general pattern is a hybrid:

```text
Raw / unstructured input
        |
        v
Semantic Agent
interpret / classify / synthesize
        |
        v
Structured state
        |
        v
Deterministic nodes
validate / calculate / route / persist
        |
        v
Semantic Agent or Skill
communicate / summarize / produce artifact
        |
        v
Output
```

This lets semantic reasoning handle ambiguity while keeping consequential calculations and state transitions explicit and auditable.

## 12. Tool and Integration Boundary

Tools are the bridge between semantic reasoning and external capabilities.

Current tool families observed include:

- export/artifact tools
- document extraction tools
- attachment/file tools
- web/search tools
- memory tools
- email/external action tools
- system-action discovery/execution
- knowledge-source tooling

Tool configuration contracts must be separated from runtime-confirmed behavior.

A documented parameter list establishes what the UI/tool contract exposes. It does not by itself prove successful execution, artifact persistence, rendering fidelity, or downstream attachment behavior.

## 13. Output Boundary

Output is the final user-visible boundary of a workflow.

A workflow may execute successfully without producing a usable user-visible response if Output is not given an explicit response source.

Runtime-confirmed examples demonstrate that explicit references such as:

```text
{{flow.startTestResponse}}
{{system.humanInput}}
```

can be consumed by Output.

Therefore:

```text
Upstream success != user-visible completion
```

The Output node must have an explicit usable response source.

## 14. Design Principles

1. Treat nodes as execution primitives with distinct responsibilities.
2. Keep business state explicit in Flow Variables.
3. Distinguish node result from persistent state mutation.
4. Keep semantic reasoning separate from deterministic computation.
5. Use Handoff as an explicit delegation contract.
6. Treat Skills as reusable capability packaging rather than just prompt text.
7. Treat Tools as concrete action boundaries.
8. Use Human Input for information collection and Approval for authorization.
9. Give Output an explicit final response source.
10. Do not infer undocumented runtime behavior from UI presence alone.
11. Promote runtime discoveries into evidence and canonical reference.
12. Keep implementation-specific orchestration examples separate from platform-wide concepts.

## 15. Evidence Boundary

This mental model is a synthesis of observed QI Studio surfaces, supplied product guidance, and controlled tests captured elsewhere in the Playbook.

Where runtime behavior is not confirmed, this document uses conceptual language rather than presenting assumptions as facts.

Supporting references:

- `02-Product-Model/Node-Taxonomy.md`
- `02-Product-Model/Variable-and-State-Model.md`
- `02-Product-Model/Agent-Capability-Model.md`
- `02-Product-Model/Tool-and-Integration-Model.md`
- `03-Canonical-Reference/Nodes/Agent.md`
- `03-Canonical-Reference/Nodes/Script.md`
- `03-Canonical-Reference/Nodes/Human-Input.md`
- `04-Evidence/Runtime/Start-HumanInput-Output-E2E.md`
- `04-Evidence/Tool-Contracts/Agent-Tool-Catalog.md`
