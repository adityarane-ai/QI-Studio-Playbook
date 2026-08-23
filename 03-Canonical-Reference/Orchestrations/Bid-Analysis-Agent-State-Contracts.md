# Bid Analysis Agent State Contracts

> **Status:** Working design baseline derived from the current QI Studio build.
> **Evidence status:** Design + observed UI configuration. Full runtime semantics remain open until controlled tests are completed.

## 1. Purpose

Define the shared Flow Variable contract for the Bid Analysis Agent so that each business object has one intentional producer and downstream nodes consume stable structures.

## 2. State Table

| Variable | Type | Purpose | Producer | Key consumers |
|---|---|---|---|---|
| `flow.conversationState` | object | Master run context | workflow/Master | Master |
| `flow.runId` | string | unique run identifier | Start | all nodes |
| `flow.currentStage` | string | current workflow phase | stage-owning nodes | Master/reporting |
| `flow.errorState` | object | structured error state | error-owning layer | Master/Output |
| `flow.fileIntake` | object | file discovery result | discovery layer | Master/Criteria/Supplier |
| `flow.criteria` | object | Criteria Specialist result | Criteria/Master integration | validation/canonicalization/Master |
| `flow.suppliers` | object | Supplier Specialist result | Supplier/Master integration | validation/canonicalization/Evaluation |
| `flow.knowledgeContext` | object | GEP context and provenance | knowledge layer | Master/Evaluation/Report |
| `flow.clarificationPackage` | object | proposed configuration package | Master | Human Input/Configuration Validation |
| `flow.evaluationConfiguration` | object | human-approved evaluation rules | Configuration Validation | Questionnaire Validation/Canonical Mapping/Evaluation/Execution |
| `flow.confirmationStatus` | string | approval state | Configuration Validation | Master/validation |
| `flow.confirmationNotes` | object/array | human confirmation notes | Configuration Validation | Master/audit |
| `flow.validationResult` | object | structural validation result | Questionnaire Validation | Canonical Mapping/Master |
| `flow.canonicalQuestionMap` | object | normalized question × supplier evaluation model | Canonical Mapping | Evaluation/Knockout/Score Validation/Report |
| `flow.scoringResult` | object | semantic score recommendations | Evaluation Specialist | Score Validation/Result Builder/Report |
| `flow.evaluationEvidence` | object | evidence references used in evaluation | Evaluation | Master/Report |
| `flow.evaluationQC` | object | Master challenge/QC result | Master | Execution & Reporting |
| `flow.knockoutResult` | object | deterministic confirmed knockout result | Knockout Script | Score Validation/Ranking/Result Builder |
| `flow.scoreValidationResult` | object | deterministic score validation/calculation result | Score Validation Script | Weighted Score/Result Builder |
| `flow.weightedScores` | object | deterministic weighted results | Weighted Score Script | Ranking/Result Builder |
| `flow.rankingResult` | object | deterministic supplier ranking | Ranking Script | Result Builder/Report |
| `flow.evaluationResult` | object | final authoritative assembled evaluation | Result Builder | Execution & Reporting/Output/Report Skill |
| `flow.report` | object | generated report artifact/reference | reporting layer | Output |
| `flow.reportStatus` | string | report generation state | reporting layer | Output |
| `flow.evaluationScenario` | object | alternative scenario/lineage | scenario layer | configuration/analysis |
| `flow.qaContext` | object | post-evaluation QA context | QA layer | Master/report |

## 3. Native/System Inputs Used

Observed system values used by this workflow include:

- `system.userQuery`
- `system.attachments`
- `system.uiAction`
- `system.sessionId`
- `system.humanInput`

The current build deliberately keeps `system.humanInput` as immediate HITL response state. It is not treated as the final approved evaluation configuration.

## 4. Start Initialization

Current Start initialization baseline:

```text
system.userQuery ← system message
system.attachments ← system attachments
system.uiAction ← system UI action
system.sessionId ← current invocation sessionId
flow.runId ← system.sessionId
flow.currentStage ← "intake"
```

The exact runtime expression syntax for some system values is UI-generated; use the dynamic picker rather than literal strings where appropriate.

## 5. Human Input

Human Input captures the response into:

```text
system.humanInput
```

The node does not directly write `flow.evaluationConfiguration`.

The intended flow is:

```text
flow.clarificationPackage
        ↓
Human Input
        ↓
system.humanInput
        ↓
Configuration Validation
        ↓
flow.evaluationConfiguration
```

## 6. Configuration Validation Contract

Inputs:

```text
flow.clarificationPackage
system.humanInput
flow.runId
flow.currentStage
```

Outputs:

```text
validationStatus
confirmationStatus
evaluationConfiguration
confirmationNotes
errors
warnings
runId
```

State updates:

```text
flow.evaluationConfiguration ← {{nodes.configuration_validation.result.evaluationConfiguration}}
flow.confirmationStatus      ← {{nodes.configuration_validation.result.confirmationStatus}}
flow.confirmationNotes      ← {{nodes.configuration_validation.result.confirmationNotes}}
```

Current first-pass human responses:

- `APPROVE` → approved configuration
- `CORRECT` → correction required, no silent interpretation
- `REJECT` → rejected configuration

Automatic correction loops remain open for later runtime design.

## 7. Questionnaire Validation Contract

Inputs:

```text
flow.criteria
flow.suppliers
flow.evaluationConfiguration
flow.runId
```

Output contract:

```json
{
  "valid": true,
  "errors": [],
  "warnings": [],
  "missingQuestions": [],
  "extraQuestions": [],
  "mappingIssues": [],
  "configurationIssues": [],
  "sourceIssues": []
}
```

State update:

```text
flow.validationResult ← {{nodes.questionnaire_validation.result}}
```

## 8. Canonical Mapping Contract

Inputs:

```text
flow.criteria
flow.suppliers
flow.evaluationConfiguration
flow.validationResult
flow.runId
```

Output:

```json
{
  "questions": [
    {
      "questionId": "",
      "questionNumber": null,
      "sectionName": "",
      "questionText": "",
      "supplierId": "",
      "supplierName": "",
      "supplierAnswer": null,
      "answered": false,
      "mappingConfidence": 1.0,
      "criteria": {
        "weight": null,
        "guidance": null,
        "scoringRubric": null,
        "knockoutConfirmed": false,
        "acceptanceCondition": null
      },
      "source": {
        "criteriaSource": {},
        "supplierSource": {}
      }
    }
  ]
}
```

State update:

```text
flow.canonicalQuestionMap ← {{nodes.canonical_mapping.result}}
```

The current workflow design also sets:

```text
flow.currentStage = "evaluation"
```

after successful canonicalization so that the same Master Agent can be re-entered in evaluation mode.

## 9. Knockout Result Contract

Canonical result:

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

State update:

```text
flow.knockoutResult ← {{nodes.knockout_evaluation.result}}
```

## 10. Score Validation Contract

Inputs:

```text
flow.evaluationConfiguration
flow.canonicalQuestionMap
flow.scoringResult
flow.knockoutResult
```

Output/state:

```text
flow.scoreValidationResult
```

The validation stage verifies approved score range, question membership, deterministic raw/section calculations and error status. It does not apply weights.

## 11. Weighted Score Contract

Inputs:

```text
flow.evaluationConfiguration
flow.scoreValidationResult
flow.knockoutResult
flow.runId
```

Output/state:

```text
flow.weightedScores
```

The calculation uses only approved weights from the configuration.

## 12. Ranking Contract

Inputs:

```text
flow.weightedScores
flow.knockoutResult
flow.runId
```

Output/state:

```text
flow.rankingResult ← {{nodes.ranking.result}}
```

Qualified suppliers are ranked by deterministic overall weighted score. Disqualified suppliers remain separate and do not receive a qualified rank.

## 13. Final Evaluation Result Contract

Result Builder inputs:

```text
flow.runId
flow.evaluationConfiguration
flow.canonicalQuestionMap
flow.scoringResult
flow.knockoutResult
flow.scoreValidationResult
flow.weightedScores
flow.rankingResult
```

Result Builder owns:

```text
flow.evaluationResult
```

State reference:

```text
{{nodes.result_builder.result}}
```

## 14. Reporting State

The reporting layer owns:

```text
flow.report
flow.reportStatus
```

The report is produced by the `bid-analysis-report-generator` Skill, which bundles `Export Excel V2`.

## 15. Expression Rule

For Script nodes, use dynamic expression references rather than pasted labels.

Example:

```text
{{nodes.ranking.result}}
```

For a specific field:

```text
{{nodes.configuration_validation.result.evaluationConfiguration}}
```

A UI token generated by the QI Studio expression picker is preferred over manually typed text.

The visible UI label `Current Node / result` is a selector concept. It should not be pasted literally as ordinary text.

## 16. Type Discipline

Use native QI Studio parameter types wherever possible:

- `string`
- `number`
- `boolean`
- `object`
- `array<string>`
- `array<number>`
- `array<boolean>`
- `array<object>`
- `array<array>`

Keep objects as objects and arrays as arrays. Do not stringify business objects merely to move them between nodes.

## 17. State Ownership Principle

A shared Flow Variable should have one intentional producer.

Agents should not directly overwrite Script-owned deterministic variables.

Handoff tools should return specialist output to the calling Agent; the calling Agent or designated integration step then persists the appropriate business state.

## 18. Verification Boundary

Runtime still needs to prove:

- exact expression resolution for nested object references
- state update ordering
- type checking on state updates
- same-Agent re-entry behavior
- whether structured Agent output remains available after re-entry
- artifact shape in `flow.report`
- Output handling of generated report artifacts
