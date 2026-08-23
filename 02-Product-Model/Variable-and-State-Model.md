# Variable and State Model

> **Status:** Current conceptual model based on observed UI, supplied guidance, and runtime-confirmed examples. Detailed scope precedence, transactionality and persistence semantics remain open unless explicitly marked.

## 1. Why State Needs Its Own Model

A QI Studio workflow has at least three different concepts that are easy to confuse:

```text
Where execution goes    -> graph / edges
What a node produced    -> node result
What the workflow keeps -> explicit workflow state
```

Conversation history, runtime metadata and long-term memory are additional concepts and should not be substituted for explicit workflow state.

## 2. State Domains

| Domain | Purpose | Typical use |
|---|---|---|
| Flow | User-created business/workflow state | IDs, configuration, intermediate objects, final results |
| System | Platform-managed values | user input, files, attachments, system metadata |
| Node result | Immediate node output | downstream reference to what a node just returned |
| Conversation/context | Context for semantic execution | prior messages and conversational material |
| Runtime context | Execution metadata | workflow/runtime identity and platform execution information |
| Long-term memory | Durable remembered information | information intended to persist beyond immediate execution |

## 3. Flow Variables

Flow Variables are explicit user-created workflow state.

Examples observed in the current Bid Analysis implementation include:

```text
flow.runId
flow.currentStage
flow.criteria
flow.suppliers
flow.knowledgeContext
flow.clarificationPackage
flow.evaluationConfiguration
flow.validationResult
flow.canonicalQuestionMap
flow.scoringResult
flow.evaluationQC
flow.knockoutResult
flow.scoreValidationResult
flow.weightedScores
flow.rankingResult
flow.evaluationResult
flow.report
```

The important platform-level rule is that Flow Variables should hold information that must be explicitly carried between execution boundaries.

## 4. System Variables

System values are platform-managed.

Observed examples include:

```text
system.humanInput
system.files
system.attachments
system.userQuery
```

The Human Input runtime tests confirmed that a response can be stored in `system.humanInput` and consumed downstream with an explicit reference.

## 5. Node Results

A node result is the direct output of that node execution.

For Script nodes, the return object becomes the node's result.

Conceptually:

```text
return {
  score: 4,
  rationale: "..."
}
        |
        v
nodes.<nodeName>.result
```

Field-level references use the platform expression mechanism, for example:

```text
{{nodes.configuration_validation.result.evaluationConfiguration}}
```

and:

```text
{{nodes.ranking.result}}
```

## 6. Node Result vs State Update

These are not the same operation.

```text
Script return
    -> node result

State Update
    -> explicit persistent workflow-state mutation
```

A node can return a rich result object without automatically making every field a Flow Variable.

State Updates should therefore be used deliberately for values that must survive into later graph stages.

## 7. Recommended Ownership Pattern

A useful rule is one primary producer per important Flow Variable.

Example:

| State | Primary producer |
|---|---|
| `flow.clarificationPackage` | Agent/orchestration layer |
| `flow.evaluationConfiguration` | Configuration Validation |
| `flow.validationResult` | Questionnaire Validation |
| `flow.canonicalQuestionMap` | Canonical Mapping |
| `flow.evaluationQC` | Master semantic QC |
| `flow.knockoutResult` | Knockout Script |
| `flow.scoreValidationResult` | Score Validation Script |
| `flow.weightedScores` | Weighted Score Script |
| `flow.rankingResult` | Ranking Script |
| `flow.evaluationResult` | Result Builder |
| `flow.report` | report-generation/export layer |

The exact producer model is an implementation pattern, not yet a fully runtime-verified platform invariant.

## 8. State Transitions

State becomes useful when paired with graph transitions.

```text
Node A
  |
  +--> result
  |
  +--> State Update
          |
          v
      flow.someState
          |
          v
Node B consumes flow.someState
```

This allows a node to expose an immediate output while also publishing a deliberate shared state contract.

## 9. Conversation History Is Not Business State

Conversation history can provide context to an Agent, but it should not be relied on as the canonical store for structured workflow objects.

For example, an approved evaluation configuration belongs in explicit workflow state rather than only inside a previous Agent message.

## 10. Runtime Context Is Not Business State

Runtime metadata is useful for execution identity and platform behavior, but should not be used as the primary storage location for business objects.

Examples include workflow identity and other execution metadata exposed by the runtime.

## 11. Long-Term Memory Is Not Workflow State

Long-term memory and Flow Variables solve different problems.

```text
Flow state
    = current workflow's explicit business state

Long-term memory
    = durable remembered information
```

Do not use long-term memory as a substitute for explicit state transitions.

The exact persistence and retrieval timing of long-term memory remains an open runtime verification area.

## 12. Expression Model

Current observed expression patterns include:

### Flow

```text
{{flow.someVariable}}
```

### System

```text
{{system.humanInput}}
```

### Node result

```text
{{nodes.<nodeName>.result}}
```

### Node result field

```text
{{nodes.<nodeName>.result.<field>}}
```

This expression system is an important part of the platform contract because it connects node execution outputs with downstream configuration.

## 13. State Mutation Questions Still Open

The platform exposes State Update operations including Set, Append, Extend and Clear. Configuration presence is established, but full runtime semantics remain to be tested for:

- transactionality
- ordering relative to node completion
- type replacement behavior
- object mutation
- array mutation
- concurrent writers
- re-entry behavior
- variable-scope precedence
- failure/rollback behavior

These belong in the Verification layer, not as assumptions in this document.

## 14. General Design Principle

The simplest reliable mental model is:

```text
Graph answers: "where?"
State answers: "what?"
Node result answers: "what did this node return?"
Conversation context answers: "what conversational information is available?"
Runtime context answers: "what execution is happening?"
Long-term memory answers: "what should persist?"
```

Keep these boundaries explicit.
