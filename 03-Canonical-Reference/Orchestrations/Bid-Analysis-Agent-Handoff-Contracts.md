# Bid Analysis Agent Handoff Contracts

> **Status:** Working design baseline from the current QI Studio implementation.
> **Evidence status:** Observed UI configuration + design; runtime serialization/aggregation remains open until tests are completed.

## 1. Purpose

Document the explicit Handoff tool contracts used by the Bid Analysis Agent. Handoff is treated as the delegation mechanism between a calling Agent and a specialist/downstream node.

The current implementation favors explicit typed parameters over putting the entire delegation contract into edge context transfer.

## 2. General Handoff Pattern

```text
Calling Agent
    ↓
Handoff Tool
    ├── task
    ├── relevant source/state
    ├── constraints
    └── fixed expected_output contract
    ↓
Target Agent / node
    ↓
Return context
    ↓
Calling Agent
```

Return edges between Deep Agents use the current baseline:

```text
Pass context: ON
Transfer type: Last message (append)
```

Full conversation transfer is intentionally avoided for specialist returns unless a specific runtime test proves it necessary.

## 3. Criteria Handoff

### Tool

`handoff_to_criteria_agent`

### Target

`criteria_deep_agent`

### Description

Delegate RFP and evaluation-framework analysis to the Criteria Deep Agent. Provide the relevant task, source files, known context, constraints, and expected structured output.

### Parameters

| Name | Type | Required | Runtime behavior |
|---|---|---|---|
| `task` | string | Yes | Master dynamically decides |
| `source_files` | array<object> | Yes | Master dynamically selects relevant files |
| `known_context` | object | No | Master provides relevant established context |
| `constraints` | array<string> | No | Master provides task-specific constraints |
| `expected_output` | string | Yes | Fixed contract |

### Fixed expected output baseline

```text
Return a structured Criteria Analysis Contract containing:
- evaluation framework
- evaluation sections
- criteria
- questions and sub-questions
- scoring methodology
- scoring scale
- weights
- mandatory requirements
- candidate knockout conditions
- exclusions or non-scored items
- ambiguities
- missing information
- source references
- confidence and interpretation status

Do not return authoritative knockout decisions or final weighted scores.
Return structured JSON.
```

## 4. Supplier Handoff

### Tool

`handoff_to_supplier_agent`

### Target

`supplier_deep_agent`

### Description

Delegate supplier response and evidence analysis to the Supplier Deep Agent. Provide the relevant supplier files, task, known context, source-first constraints, and expected structured output.

### Parameters

| Name | Type | Required | Runtime behavior |
|---|---|---|---|
| `task` | string | Yes | Master dynamically decides |
| `source_files` | array<object> | Yes | Master dynamically selects relevant supplier files |
| `known_context` | object | No | Master provides relevant established context |
| `constraints` | array<string> | No | Master provides source-first constraints |
| `expected_output` | string | Yes | Fixed contract |

### Fixed expected output baseline

```text
Return a structured Supplier Evidence Contract containing:
- identified suppliers
- supplier responses
- question-level response mappings
- supporting evidence
- source references
- missing responses
- ambiguous responses
- conflicting information
- strengths or notable claims only where supported by source evidence
- evidence confidence
- unresolved information gaps

Do not infer or invent supplier facts.
Do not supplement supplier responses using external knowledge.
Preserve source provenance.
Do not perform final scoring, weighting, ranking, or knockout decisions.
```

## 5. Evaluation Handoff

### Tool

`handoff_to_evaluation_agent`

### Target

`evaluation_deep_agent`

### Description

Delegate qualitative supplier evaluation to the Evaluation Deep Agent after human-confirmed configuration and canonical evaluation data are available.

### Parameters

| Name | Type | Required | Runtime mapping |
|---|---|---|---|
| `task` | string | Yes | Master dynamically decides |
| `evaluation_configuration` | object | Yes | `{{flow.evaluationConfiguration}}` |
| `canonical_evaluation_model` | object | Yes | `{{flow.canonicalQuestionMap}}` |
| `supplier_evidence` | object | Yes | `{{flow.suppliers}}` |
| `knowledge_context` | object | No | `{{flow.knowledgeContext}}` when relevant |
| `constraints` | array<string> | No | Master dynamically decides |
| `expected_output` | string | Yes | Fixed contract |

### Fixed expected output baseline

```text
Return a structured Semantic Evaluation Contract containing:
- supplier
- evaluation section
- criterion
- question or evaluation item
- score recommendation
- score rationale
- supporting supplier evidence
- source references
- relevant knowledge references where used
- strengths
- weaknesses
- risks
- evidence gaps
- confidence
- comparison observations where relevant

Scores must follow the human-approved evaluation configuration.
Do not perform authoritative knockout execution, score arithmetic, weighting, or ranking.
Do not modify the evaluation configuration.
Return structured JSON only.
```

## 6. Execution & Reporting Handoff

### Tool

`handoff_to_execution_and_reporting_agent`

### Target

`execution_and_reporting_agent`

### Description

Delegate the approved evaluation to the Execution & Reporting Deep Agent for deterministic execution and final reporting.

### Parameters

| Name | Type | Required | Runtime mapping |
|---|---|---|---|
| `task` | string | Yes | Master dynamically decides |
| `evaluation_configuration` | object | Yes | `{{flow.evaluationConfiguration}}` |
| `canonical_evaluation_model` | object | Yes | `{{flow.canonicalQuestionMap}}` |
| `supplier_evidence` | object | Yes | `{{flow.suppliers}}` |
| `semantic_evaluation` | object | Yes | `{{flow.scoringResult}}` |
| `evaluation_qc` | object | Yes | `{{flow.evaluationQC}}` |
| `expected_output` | string | Yes | Fixed contract |

### Execution gate

The Master should invoke this Handoff only when:

```text
flow.evaluationQC.status = "PASS"
```

and

```text
flow.evaluationQC.readyForExecution = true
```

### Fixed expected output baseline

```text
Return a structured Execution & Reporting Result containing:
- execution status
- knockout results
- score validation result
- weighted scores
- supplier ranking
- final evaluation result
- report status
- report reference or artifact information where available
- warnings
- errors

Do not modify the human-confirmed evaluation configuration.
Deterministic outputs must remain authoritative.
Return structured JSON only.
```

## 7. Knockout Handoff

### Tool

`handoff_to_knockout_evaluation`

### Target

`knockout_evaluation` Script node

### Design decision

The current contract deliberately avoids passing semantic evaluation when the canonical execution structure already contains the supplier answer and confirmed knockout configuration needed by the Script.

### Parameters

| Name | Type | Required | Runtime mapping |
|---|---|---|---|
| `task` | string | Yes | Execution & Reporting Agent dynamically decides |
| `evaluation_configuration` | object | Yes | `{{flow.evaluationConfiguration}}` |
| `canonical_question_map` | object | Yes | `{{flow.canonicalQuestionMap}}` |
| `run_id` | string | Yes | `{{flow.runId}}` |
| `expected_output` | string | Yes | Fixed contract |

### Fixed expected output baseline

```text
Return the deterministic Knockout Result containing suppliers with supplierId, supplierName, qualified, status, failedRules, ambiguousRules, and decisions.
Allowed statuses are PASS, FAIL, AMBIGUOUS, and NOT_APPLICABLE.
Execute only confirmed knockout rules.
Do not create, modify, infer, or remove knockout rules.
Do not perform score calculation, weighting, or supplier ranking.
```

## 8. Handoff Tool Configuration Principles

1. Use native QI Studio parameter types rather than stringifying objects.
2. Keep `expected_output` fixed where downstream parsing should be stable.
3. Keep task/context/file selections dynamic where the caller needs to adapt to the run.
4. Do not use Handoff State Update as a shortcut for business-variable ownership.
5. Let the target node/Agent produce its result, then persist the appropriate business state in the designated producer/integration step.
6. Keep return context focused on the specialist's final result.
7. Do not assume tool-result serialization is fully runtime-proven until tested.

## 9. Return Edge Baseline

For specialist Deep Agent return edges currently use:

```text
Pass context: ON
Transfer type: Last message (append)
```

This means the returned specialist result is appended into the caller's context rather than replacing the caller's conversation.

## 10. Open Verification Items

- Exact Handoff parameter serialization into target Agent input context.
- Whether array/object Handoff parameters preserve type exactly at runtime.
- Exact return-context envelope for structured specialist output.
- Whether the same calling Master node can reliably distinguish workflow phases after re-entry.
- Interaction between Handoff parameter payload and Agent user message.
- Partial failure and retry semantics for Handoff execution.
- Handoff behavior when target execution returns `success=false`.
