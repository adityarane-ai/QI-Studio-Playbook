# Bid Analysis Agent Deterministic Execution

> **Status:** Working implementation baseline.
> **Evidence status:** Design + observed QI Studio Script UI; runtime execution still requires controlled testing.

## 1. Purpose

Define the deterministic Script chain that converts approved semantic evaluation output into authoritative qualification, weighted scores, ranking and final evaluation results.

## 2. Deterministic Chain

```text
Execution & Reporting Deep Agent
        ↓
Handoff → Knockout Evaluation
        ↓
Score Validation
        ↓
Weighted Score
        ↓
Ranking
        ↓
Result Builder
        ↓
flow.evaluationResult
```

The Agent may orchestrate this chain but does not replace the deterministic calculations.

## 3. Knockout Evaluation

### Purpose

Execute only confirmed knockout rules against the canonical supplier evaluation data.

### Inputs

```text
evaluationConfiguration : object
canonicalQuestionMap     : object
runId                    : string
```

Runtime mappings:

```text
{{flow.evaluationConfiguration}}
{{flow.canonicalQuestionMap}}
{{flow.runId}}
```

### Preconditions

- `evaluationConfiguration.approved === true`
- canonical question map is present
- confirmed knockout rules are available or the list is empty

### Deterministic rule

If a knockout rule cannot be safely evaluated because an acceptance condition, question mapping or value is missing/unsupported, return `AMBIGUOUS` rather than guessing.

### Output

```json
{
  "suppliers": [
    {
      "supplierId": "",
      "supplierName": "",
      "qualified": true,
      "status": "PASS",
      "failedRules": [],
      "ambiguousRules": [],
      "decisions": []
    }
  ]
}
```

Allowed statuses:

- `PASS`
- `FAIL`
- `AMBIGUOUS`
- `NOT_APPLICABLE`

### State update

```text
flow.knockoutResult ← {{nodes.knockout_evaluation.result}}
```

## 4. Score Validation

### Purpose

Validate semantic score recommendations against the approved evaluation configuration and produce deterministic raw/section score calculations.

### Inputs

```text
evaluationConfiguration : object
canonicalQuestionMap     : object
scoringResult            : object
knockoutResult           : object
```

### Validation responsibilities

- verify approved configuration
- verify scoring scale
- verify score is numeric
- verify score lies within approved range
- verify question exists in canonical map
- preserve rationale/evidence/source information
- calculate raw question/section totals
- surface invalid score items
- preserve knockout qualification context

### Explicitly excluded responsibilities

- applying weights
- supplier ranking
- changing knockout decisions
- semantic reinterpretation

### State update

```text
flow.scoreValidationResult ← {{nodes.score_validation.result}}
```

## 5. Weighted Score

### Purpose

Apply the approved evaluation weights to validated scores.

### Inputs

```text
evaluationConfiguration : object
scoreValidationResult   : object
knockoutResult          : object
runId                   : string
```

### Calculation baseline

For each section:

```text
raw section score
÷
maximum section score
× 100
=
section achievement %

section achievement %
×
approved section weight
=
weighted contribution
```

Overall weighted score is the sum of weighted section contributions.

### Weight authority

Weights come only from `flow.evaluationConfiguration`.

The Script must not infer or modify weights.

### State update

```text
flow.weightedScores ← {{nodes.weighted_score.result}}
```

### Expected status

Approved weights must be structurally valid. The baseline implementation expects percentage weights to sum to 100.

## 6. Ranking

### Purpose

Rank qualified suppliers using deterministic overall weighted scores.

### Inputs

```text
weightedScores : object
knockoutResult  : object
runId           : string
```

### Rules

1. Only suppliers with deterministic qualification status `PASS` are eligible for qualified ranking.
2. Suppliers with `FAIL` or `AMBIGUOUS` knockout status do not receive a qualified rank.
3. Suppliers with invalid deterministic score calculations do not receive a qualified rank.
4. Highest overall weighted score ranks first.
5. Ties use standard competition ranking unless an approved methodology specifies a different tie rule.

### State update

```text
flow.rankingResult ← {{nodes.ranking.result}}
```

### Output baseline

```json
{
  "runId": "",
  "rankings": [],
  "disqualifiedSuppliers": [],
  "tieHandling": ""
}
```

## 7. Result Builder

### Purpose

Assemble the final authoritative evaluation result from already-owned deterministic and semantic outputs. Do not recalculate business results here.

### Inputs

```text
runId
 evaluationConfiguration
canonicalQuestionMap
scoringResult
knockoutResult
scoreValidationResult
weightedScores
rankingResult
```

### Output/state

```text
flow.evaluationResult ← {{nodes.result_builder.result}}
```

### Result responsibilities

The final object should preserve:

- run ID
- scenario/configuration lineage where available
- supplier qualification
- supplier ranking
- weighted scores
- score validation status
- semantic evaluation
- knowledge references where applicable
- audit/source references
- assumptions/configuration metadata

## 8. Error Handling Policy

The current build keeps Script Error Handling OFF during initial construction and test development.

The reason is observability: deterministic failures should be visible while the workflow contracts are still being proven.

Automatic retry/recovery is a later design decision and should not be introduced before the failure semantics are understood.

## 9. State Ownership Rule

The deterministic chain uses one primary producer per result object:

```text
Knockout Evaluation → flow.knockoutResult
Score Validation    → flow.scoreValidationResult
Weighted Score      → flow.weightedScores
Ranking             → flow.rankingResult
Result Builder      → flow.evaluationResult
```

Agents should consume these results and should not independently overwrite them.

## 10. Runtime Verification Required

The deterministic chain still needs runtime proof with realistic data for:

- Script input object typing
- nested object access
- state update expression resolution
- score-range validation
- empty knockout behavior
- ambiguous knockout behavior
- weight-total validation
- ranking tie behavior
- downstream state availability after each Script
- Result Builder completeness
