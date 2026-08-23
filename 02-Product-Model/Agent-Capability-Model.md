# Agent Capability Model

> **Status:** Current platform model based on observed QI Studio UI, supplied product guidance, and selected runtime-confirmed behavior. Configuration presence is separated from runtime semantics.

## 1. Agent as a Platform Primitive

The Agent node is the primary semantic execution primitive in QI Studio.

It should be understood as a configurable execution boundary that combines:

```text
Strategy
+ Model
+ Messages
+ Context
+ Capabilities
+ Memory
+ State
+ Output
+ Guardrails / Error handling
```

The Agent is appropriate when the task requires interpretation, synthesis, judgement, generation, or flexible orchestration.

It should not be the default replacement for deterministic primitives.

## 2. Strategy

Observed Agent strategies include:

- `ReAct`
- `Deep Agent`

Strategy affects how the Agent reasons and interacts with its capabilities.

The Deep Agent surface exposes subagent controls. The current UI observation shows a visible maximum of three parallel subagents. Scheduling, aggregation, failure semantics and exact parallelism remain runtime verification items.

## 3. Model Selection

The Agent allows a model to be selected as part of the semantic execution configuration.

Model choice should follow the task's reasoning, latency, cost and context requirements.

The platform model contract should distinguish:

```text
model selection
!=
workflow business logic
```

Changing the model should not require changing deterministic state contracts.

## 4. Messages

Observed message roles include:

- `system`
- `user`
- `assistant`

Messages define the semantic instructions/context presented to the Agent.

A useful separation is:

```text
System message
    = persistent role, rules, authority boundaries

User message
    = task framing for the current invocation

Handoff parameters / Flow state
    = structured runtime inputs
```

Do not force complex business-state contracts into free-form prompts when explicit Flow Variables or typed tool parameters can carry them.

## 5. Response Format

Observed response format choices include Plain Text and JSON.

JSON is preferable when the Agent's response is an inter-stage contract consumed by another node or process.

The output schema should be treated as an explicit contract rather than an informal formatting request.

## 6. Tools

Tools give an Agent concrete actions.

Examples in the broader platform include:

- document extraction
- web search
- Excel export
- PowerPoint export
- PDF export
- Word export
- HTML export
- email
- system actions

A tool should have a typed parameter contract and a defined output/result path.

Use tools for actions; use Flow state for durable workflow state.

## 7. Libraries

Libraries provide reusable contextual or knowledge capabilities available to an Agent.

They should be distinguished from direct action tools.

A Library can influence what knowledge/context an Agent can use without being equivalent to a deterministic workflow node.

Exact Library runtime loading and retrieval semantics remain an evidence/testing area unless explicitly confirmed.

## 8. Skills

A Skill is a reusable capability package for an Agent.

A Skill can contain:

- domain-specific instructions
- operating procedures
- report-generation or task-specific methodology
- bundled tools

A useful conceptual pattern is:

```text
Agent
  |
  +-- Skill
        |
        +-- Tool(s)
```

This allows specialized capability instructions to remain modular rather than being embedded in the Agent's global system prompt.

The report-generation work demonstrated that a Skill can be designed as the owner of presentation/report logic while an Export tool remains the concrete renderer.

Bundled Skill-tool runtime behavior remains an important verification item.

## 9. Widgets

Widgets are UI-oriented capabilities that can support an Agent experience or interaction surface.

They should be treated as a distinct capability category rather than confused with tools or state variables.

Exact widget execution/output semantics are outside the current confirmed baseline.

## 10. Connectors

Connectors provide integration with external or connected systems.

They are capability boundaries, not semantic reasoning themselves.

A clean mental model is:

```text
Agent reasons
   |
   +--> Tool performs an action
   +--> Connector accesses an external system
   +--> Skill provides specialized operating knowledge
   +--> Library provides reusable knowledge/context
```

## 11. Context Management

Observed advanced controls include context scope and context strategies.

The current working Agent baseline used during orchestration construction is:

```text
Context Management: ON
Scope: Tool results only
Strategy: Replace
Threshold: Tokens
```

This is a configuration baseline, not yet a full runtime semantic guarantee.

The exact meaning of threshold accounting, message replacement/drop behavior and context retention remains an open runtime-verification area.

## 12. Long-Term Memory

Long-term Memory is distinct from current Flow state and conversation history.

The Agent UI exposes memory controls.

Current build baseline:

```text
Long-term Memory: OFF
```

The conceptual separation is:

```text
Flow state
    = current workflow business state

Conversation context
    = current conversational information

Long-term memory
    = durable remembered information
```

Persistence and retrieval timing remain open until runtime-tested.

## 13. State Update

The Agent can perform State Updates in addition to returning an Agent response.

The key distinction is:

```text
Agent response
    -> node output/result

Agent State Update
    -> explicit workflow-state mutation
```

State should be used for cross-node business/workflow data that must survive the immediate semantic response.

Exact transactionality, ordering, overwrite behavior and scope precedence remain runtime verification items.

## 14. Output Variables

The Agent exposes output variables and structured output capabilities.

Observed Agent outputs include concepts such as:

- `text`
- `toolCalls`
- `structuredOutput`
- `success`
- `error`
- `error.message`
- `error.status_code`

These outputs allow an Agent to serve as an inter-stage contract.

When a downstream node needs a specific structured field, prefer explicit structured output over parsing free-form text.

## 15. Error Handling

The Agent UI exposes Error Handling controls.

Current initial-build baseline:

```text
Error Handling: OFF
```

This is not a claim that Error Handling is unnecessary. It means the early platform construction has deliberately kept the recovery behavior simple until the exact runtime semantics are proven.

The following remain open:

- retry semantics
- fallback behavior
- recovery path
- interaction with State Updates
- tool failure propagation
- partial-failure behavior in Deep Agent workflows

## 16. Guardrails

Guardrails are a distinct safety/policy boundary around semantic execution.

Do not silently treat prompt instructions as a replacement for a Guardrail where the platform provides a dedicated control.

Exact Guardrail runtime behavior needs dedicated evidence.

## 17. Deep Agent and Subagents

Deep Agent is a strategy with explicit subagent/delegation capabilities.

Conceptually:

```text
Deep Agent
   |
   +--> specialist subagent
   +--> specialist subagent
   +--> specialist subagent
   |
   +--> aggregation / synthesis
```

The current UI exposes a visible maximum of three parallel subagents.

The following require runtime confirmation:

- whether three is a hard runtime maximum
- scheduling behavior
- aggregation semantics
- order of returned results
- partial failure handling
- context sharing
- state sharing
- nested subagent behavior

## 18. Handoff as an Agent Capability

Handoff should be treated as a delegation contract, not merely a graph edge.

Recommended pattern:

```text
Caller Agent
    ↓
Handoff tool
    ↓
Target Agent
    ↓
Return context
    ↓
Caller Agent
```

The caller should provide explicit typed parameters such as:

- task
- relevant source/state
- constraints
- expected output contract

The current working baseline for Deep Agent return edges is:

```text
Pass context: ON
Transfer type: Last message (append)
```

Runtime serialization and aggregation semantics remain open.

## 19. Agent Design Rule

Use the Agent where semantic judgement is the hard part.

Use deterministic primitives where exactness is the hard part.

A useful pattern is:

```text
Unstructured input
      ↓
Agent: interpret
      ↓
Structured output/state
      ↓
Rule / Script / Compute: deterministic processing
      ↓
Agent or Skill: communicate / generate
```

## 20. Capability Ownership

Do not overload the Agent with every available capability.

Expose only what the task needs.

A clean implementation should be explainable as:

```text
Agent
├── system role
├── user task
├── required tools
├── relevant Skill(s)
├── relevant Library/knowledge
├── minimal context
├── explicit state contract
└── explicit output contract
```

## 21. Evidence Boundary

The presence of a configuration control proves that the capability exists in the UI. It does not automatically prove exact runtime behavior.

Runtime-confirmed observations currently include selected Human Input/Output interactions and other documented tests elsewhere in the Playbook. Agent advanced semantics remain partly open.

Supporting references:

- `03-Canonical-Reference/Nodes/Agent.md`
- `03-Canonical-Reference/Nodes/Human-Input.md`
- `03-Canonical-Reference/Nodes/Script.md`
- `04-Evidence/Tool-Contracts/Agent-Tool-Catalog.md`
- `05-Verification/Verification-Queue.md`
