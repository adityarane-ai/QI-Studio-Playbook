# Bid Analysis Agent Script Contracts

> **Status:** Working implementation baseline.
> **Evidence status:** Configuration/design derived from the current QI Studio build; runtime execution of the full contracts remains open until controlled tests are completed.
> **Purpose:** Canonical implementation reference for the deterministic Script nodes used by the RFP Qualitative Bid Analysis Agent.

## 1. Deterministic Boundary

The Script layer begins after the semantic Evaluation Deep Agent has produced an evaluation recommendation that has passed Master QC.

```text
Semantic Evaluation
      ↓
Master QC
      ↓
Execution & Reporting
      ↓
Knockout Evaluation
      ↓
Score Validation
      ↓
Weighted Score
      ↓
Ranking
      ↓
Result Builder
```

The Script layer owns deterministic execution. It must not invent semantic judgements, modify human-approved configuration, or override supplier evidence.

## 2. Script State Ownership

| Script node | Primary output variable | Responsibility |
|---|---|---|
| Configuration Validation | `flow.evaluationConfiguration` | Turn the Human Input response and Master clarification package into the authoritative configuration contract. |
| Questionnaire Validation | `flow.validationResult` | Validate structural completeness and consistency before canonicalization. |
| Canonical Mapping | `flow.canonicalQuestionMap` | Normalize criteria and supplier responses into a canonical question-by-supplier model. |
| Knockout Evaluation | `flow.knockoutResult` | Execute confirmed knockout rules deterministically. |
| Score Validation | `flow.scoreValidationResult` | Validate semantic score recommendations and calculate raw/section scores. |
| Weighted Score | `flow.weightedScores` | Apply approved weights to validated scores. |
| Ranking | `flow.rankingResult` | Rank qualified suppliers using deterministic weighted scores. |
| Result Builder | `flow.evaluationResult` | Assemble the authoritative final evaluation result. |

## 3. Common QI Studio Script Pattern

Each Script follows the observed Script-node contract:

```text
Declared Inputs
      ↓
input.<field>
      ↓
JavaScript
      ↓
return { ... }
      ↓
Declared Return Output
      ↓
State Update → shared Flow Variable
```

The Script's returned object is the node result. Persistent workflow state is a separate State Update operation.

Use the expression form:

```text
{{nodes.<node_name>.result}}
```

For a returned field:

```text
{{nodes.<node_name>.result.<field>}}
```

The actual expression token must be created through QI Studio's dynamic expression system rather than pasted as ordinary prose.

## 4. Configuration Validation

### Purpose

Validate the human confirmation and establish the authoritative evaluation configuration.

### Inputs

| Input | Type | Runtime mapping |
|---|---|---|
| `clarificationPackage` | object | `{{flow.clarificationPackage}}` |
| `humanInput` | object | `{{system.humanInput}}` |
| `runId` | string | `{{flow.runId}}` |
| `currentStage` | string | `{{flow.currentStage}}` |

### Core behavior

- `APPROVE` should establish an approved configuration based on the Master-proposed package.
- `CORRECT` should not guess or silently modify rules from free text; it should return a correction-required result unless a structured correction loop is implemented.
- `REJECT` should block execution and preserve the rejection.
- Unknown responses should be invalid.

### Outputs

- `validationStatus`
- `confirmationStatus`
- `evaluationConfiguration`
- `confirmationNotes`
- `errors`
- `warnings`
- `runId`

### State updates

```text
flow.evaluationConfiguration
← {{nodes.configuration_validation.result.evaluationConfiguration}}

flow.confirmationStatus
← {{nodes.configuration_validation.result.confirmationStatus}}

flow.confirmationNotes
← {{nodes.configuration_validation.result.confirmationNotes}}
```

### Authority rule

This is the designated producer of `flow.evaluationConfiguration` after Human Input. Human Input itself stores the immediate response in `system.humanInput`.

## 5. Questionnaire Validation

### Purpose

Validate that criteria, supplier responses, and approved configuration are structurally usable before canonicalization.

### Inputs

```text
criteria → {{flow.criteria}}
suppliers → {{flow.suppliers}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
runId → {{flow.runId}}
```

### Core validation categories

- Criteria availability
- Supplier availability
- Question identifiers
- Duplicate questions
- Missing supplier questions
- Extra supplier questions
- Supplier IDs and names
- Source/provenance coverage
- Approved scoring scale
- Included sections
- Confirmed knockout rule references

### Outputs

```text
valid
errors
warnings
missingQuestions
extraQuestions
mappingIssues
configurationIssues
sourceIssues
```

### State update

```text
flow.validationResult
← {{nodes.questionnaire_validation.result}}
```

Do not calculate supplier scores or execute knockout logic here.

## 6. Canonical Mapping

### Purpose

Create the canonical question-by-supplier model consumed by the Evaluation Agent and deterministic execution layer.

### Inputs

```text
criteria → {{flow.criteria}}
suppliers → {{flow.suppliers}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
validationResult → {{flow.validationResult}}
runId → {{flow.runId}}
```

### Canonical record baseline

Each question-supplier record should contain, where available:

- `questionId`
- `questionNumber`
- `sectionName`
- `questionText`
- `supplierId`
- `supplierName`
- `supplierAnswer`
- `answered`
- `mappingConfidence`
- `criteria.weight`
- `criteria.guidance`
- `criteria.scoringRubric`
- `criteria.knockoutConfirmed`
- `criteria.acceptanceCondition`
- `source.criteriaSource`
- `source.supplierSource`

### State update

```text
flow.canonicalQuestionMap
← {{nodes.canonical_mapping.result}}
```

The node should also set:

```text
flow.currentStage = evaluation
```

This drives the same Master Agent into its post-canonicalization evaluation phase on re-entry.

## 7. Knockout Evaluation

### Purpose

Execute only confirmed knockout rules against canonical supplier evaluation data.

### Inputs

The current implementation should be configured to receive the canonical evaluation model, confirmed rules, supplier evidence/answers and run context required by the script.

The exact input schema should be kept aligned with the configured `Knockout Evaluation` Script rather than relying on inferred fields.

### Required behavior

For every applicable supplier and confirmed knockout rule:

1. Identify the answer/evidence relevant to the rule.
2. Evaluate the approved acceptance condition deterministically.
3. Return one of:
   - `PASS`
   - `FAIL`
   - `AMBIGUOUS`
4. Preserve the rule ID, question reference, decision, and reason.
5. Never invent evidence.
6. Never run an unconfirmed knockout rule.

### State update

```text
flow.knockoutResult
← {{nodes.knockout_evaluation.result}}
```

### Output baseline

```text
{
  "suppliers": [
    {
      "supplierId": "...",
      "supplierName": "...",
      "qualified": true,
      "status": "PASS",
      "failedRules": [],
      "ambiguousRules": [],
      "decisions": []
    }
  ]
}
```

The exact fields must match the configured Return schema.

## 8. Score Validation

### Purpose

Convert semantic score recommendations into validated deterministic raw/section scores.

### Inputs

```text
evaluationConfiguration → {{flow.evaluationConfiguration}}
canonicalQuestionMap → {{flow.canonicalQuestionMap}}
scoringResult → {{flow.scoringResult}}
knockoutResult → {{flow.knockoutResult}}
```

### Required behavior

- Require approved configuration.
- Validate every score is numeric.
- Validate scores against the approved range.
- Reject unknown question IDs.
- Preserve evaluator rationale and evidence.
- Calculate raw question/section totals.
- Calculate section-level percentages.
- Preserve knockout qualification status.
- Do not apply section weights.
- Do not rank suppliers.

### State update

```text
flow.scoreValidationResult
← {{nodes.score_validation.result}}
```

### Output baseline

```text
{
  "validationStatus": "VALID | INVALID",
  "scoreRange": {
    "min": 0,
    "max": 5
  },
  "suppliers": [],
  "errors": []
}
```

## 9. Weighted Score

### Purpose

Apply only the human-approved evaluation weights to validated raw/section scores.

### Inputs

```text
evaluationConfiguration → {{flow.evaluationConfiguration}}
scoreValidationResult → {{flow.scoreValidationResult}}
knockoutResult → {{flow.knockoutResult}}
runId → {{flow.runId}}
```

### Required behavior

For each section:

```text
Raw Section Score
÷
Maximum Section Score
× 100
=
Section Achievement Percentage
```

Then:

```text
Section Achievement Percentage
×
Approved Section Weight
=
Weighted Contribution
```

Overall weighted score is the sum of the approved section contributions.

The Script must validate that the approved weights form a valid weighting system before calculating results. For a percentage-based scheme, the expected total is 100.

### State update

```text
flow.weightedScores
← {{nodes.weighted_score.result}}
```

### Output baseline

```text
{
  "runId": "...",
  "calculationStatus": "VALID | INVALID",
  "totalApprovedWeight": 100,
  "suppliers": []
}
```

## 10. Ranking

### Purpose

Produce a deterministic qualified-supplier ranking from weighted scores and knockout status.

### Inputs

```text
weightedScores → {{flow.weightedScores}}
knockoutResult → {{flow.knockoutResult}}
runId → {{flow.runId}}
```

### Required behavior

- Rank only suppliers that are qualified by deterministic knockout results.
- Require valid deterministic weighted scores.
- Sort highest score first.
- Use a deterministic tie rule.
- Preserve disqualified suppliers in a separate collection.
- Do not give a qualified rank to a disqualified supplier.
- Do not use semantic scores directly.

### Tie rule

Current design baseline:

```text
100 → Rank 1
90  → Rank 2
90  → Rank 2
80  → Rank 4
```

This is standard competition ranking.

### State update

```text
flow.rankingResult
← {{nodes.ranking.result}}
```

### Output baseline

```text
{
  "runId": "...",
  "rankings": [],
  "disqualifiedSuppliers": [],
  "tieHandling": "..."
}
```

## 11. Result Builder

### Purpose

Assemble the authoritative final evaluation result without changing any upstream decision.

### Inputs

```text
runId → {{flow.runId}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
canonicalQuestionMap → {{flow.canonicalQuestionMap}}
scoringResult → {{flow.scoringResult}}
knockoutResult → {{flow.knockoutResult}}
scoreValidationResult → {{flow.scoreValidationResult}}
weightedScores → {{flow.weightedScores}}
rankingResult → {{flow.rankingResult}}
```

### Required behavior

- Combine the approved configuration lineage with deterministic results.
- Preserve semantic evaluator information.
- Preserve supplier qualification status.
- Preserve ranking and disqualified suppliers.
- Preserve source/evidence references where available.
- Produce a report-ready final contract.
- Do not recalculate scores.
- Do not re-rank suppliers.
- Do not change qualification decisions.

### State update

```text
flow.evaluationResult
← {{nodes.result_builder.result}}
```

### Output baseline

The final object should contain, where available:

- `runId`
- `configurationId`
- `configurationVersion`
- `scenarioId`
- `summary`
- `suppliers`
- `knowledgeReferences`
- `audit`
- `ranking`

## 12. Script Design Rules

### Inputs are contracts

Every declared input should have:

- a precise name
- an explicit type
- a description
- a runtime mapping

Do not rely on arbitrary undeclared input paths.

### Outputs are contracts

The Script return object must match the Return schema.

### State updates are separate

Keep this distinction explicit:

```text
return {...}
→ node result

State Update
→ persistent shared workflow state
```

### Determinism

A Script node in this deterministic chain should produce the same output for the same inputs.

### Error behavior

For initial build configuration, Error Handling remains OFF and errors are represented through structured return contracts or native Script failure. Runtime recovery semantics remain a verification item.

### Testing

Use realistic test values in `Test Script` before connecting the Script into the live chain.

At minimum test:

1. valid happy path
2. missing required input
3. malformed object
4. invalid type/value
5. boundary score
6. empty supplier list where applicable
7. duplicate question IDs where applicable
8. invalid configuration
9. knockout ambiguity
10. tie ranking

## 13. Ownership Invariants

The following invariants should remain true:

```text
Only Configuration Validation produces flow.evaluationConfiguration.
Only Questionnaire Validation produces flow.validationResult.
Only Canonical Mapping produces flow.canonicalQuestionMap.
Only Knockout Evaluation produces flow.knockoutResult.
Only Score Validation produces flow.scoreValidationResult.
Only Weighted Score produces flow.weightedScores.
Only Ranking produces flow.rankingResult.
Only Result Builder produces flow.evaluationResult.
```

Breaking these ownership rules increases the risk of silent state corruption and makes debugging difficult.

## 14. Runtime Verification Boundary

The Script contracts in this document are implementation baselines, not proof of runtime correctness.

The following remain open until executed:

- exact JavaScript runtime behavior
- exact missing/wrong-type input handling
- state update ordering
- nested object expression behavior
- error propagation
- retry behavior
- large collection performance
- exact downstream object serialization

When a controlled test proves an item, record the evidence in `04-Evidence/Runtime/`, update this document or the relevant canonical reference, and remove the resolved question from `05-Verification`.
