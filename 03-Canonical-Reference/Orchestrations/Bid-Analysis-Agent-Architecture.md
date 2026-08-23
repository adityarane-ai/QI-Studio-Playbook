# Bid Analysis Agent Architecture

> **Status:** Design baseline captured from the current QI Studio implementation work.
> **Evidence status:** Design + observed configuration; full end-to-end runtime semantics remain open until execution tests are completed.
> **Purpose:** Canonical architecture reference for the RFP Qualitative Bid Analysis Agent being built in QI Studio.

## 1. Objective

Build an enterprise-grade RFP qualitative bid-analysis orchestration that separates:

- source/evidence discovery
- human governance
- structural validation
- canonicalization
- semantic evaluation
- deterministic qualification and scoring
- ranking
- report generation

The architecture deliberately separates semantic AI work from deterministic processing.

## 2. Locked High-Level Architecture

```text
START
  ↓
MASTER DEEP AGENT
  │
  ├── Handoff → CRITERIA DEEP AGENT
  │                    ↓
  │                  RETURN
  │
  ├── Handoff → SUPPLIER DEEP AGENT
  │                    ↓
  │                  RETURN
  │
  ↓
HUMAN INPUT
  ↓
CONFIGURATION VALIDATION
  ↓
QUESTIONNAIRE VALIDATION
  ↓
CANONICAL MAPPING
  ↓
MASTER DEEP AGENT (same Agent node re-entered)
  │
  └── Handoff → EVALUATION DEEP AGENT
                       ↓
                    RETURN
                       ↓
                  MASTER QC
                       │
                       └── Handoff
                              ↓
                    EXECUTION & REPORTING
                         DEEP AGENT
                              │
                              └── Handoff → KNOCKOUT EVALUATION
                                             ↓
                                       SCORE VALIDATION
                                             ↓
                                        WEIGHTED SCORE
                                             ↓
                                           RANKING
                                             ↓
                                       RESULT BUILDER
                                             ↓
                                    return to Execution & Reporting
                                             ↓
                                   Bid Analysis Report Skill
                                             ↓
                                      Export Excel V2
                                             ↓
                                           OUTPUT
```

## 3. Master Agent Re-entry Model

The Master Deep Agent is the same Agent node re-entered at multiple workflow stages.

### Phase 1: Discovery / Clarification

When the run is in intake/discovery/clarification:

- inspect the request and files
- delegate criteria analysis
- delegate supplier response/evidence analysis
- integrate specialist results
- identify ambiguity and missing information
- construct the Bid Clarification Package
- prepare the Human Input gate

### Phase 2: Validation / Canonicalization

After Human Input:

- use the approved evaluation configuration
- consume validation and canonicalization results
- do not repeat Criteria/Supplier discovery unnecessarily

### Phase 3: Evaluation Orchestration

After Canonical Mapping:

- treat `flow.evaluationConfiguration` as authoritative
- treat `flow.canonicalQuestionMap` as the canonical evaluation structure
- treat `flow.suppliers` as source-faithful evidence
- hand off to Evaluation Deep Agent
- challenge returned semantic results
- create `flow.evaluationQC`
- only pass to Execution & Reporting when QC allows execution

## 4. Specialist Boundaries

### Criteria Deep Agent

Owns RFP/evaluation-framework understanding:

- evaluation sections
- criteria/questions
- scoring scale and rubric
- weights
- mandatory requirements
- candidate knockout conditions
- ambiguities
- missing information
- provenance

Does not own final human-confirmed configuration or deterministic scoring.

### Supplier Deep Agent

Owns source-faithful supplier evidence analysis:

- supplier identity
- responses
- question-level mapping
- evidence/source references
- missing/ambiguous responses
- supplier exceptions/deviations
- evidence gaps

Must not invent supplier claims or use external knowledge to fill missing supplier information.

### Evaluation Deep Agent

Owns semantic supplier evaluation against the approved configuration:

- score recommendations
- rationale
- evidence linkage
- strengths
- weaknesses
- risks
- confidence

Must not own authoritative arithmetic, weighting, ranking or knockout execution.

### Execution & Reporting Deep Agent

Owns downstream orchestration:

- start deterministic execution
- hand off to knockout execution
- allow downstream scripts to complete
- invoke the report-generation Skill once `flow.evaluationResult` exists

It is not itself the deterministic calculation engine or report-formatting engine.

## 5. Deterministic Boundary

The deterministic processing chain owns:

- confirmed knockout execution
- score validation/calculation
- weighted scoring
- ranking
- final result assembly

The Agent layers may reason about these concepts but must not override deterministic outputs.

## 6. State Ownership

Important Flow Variable ownership:

| Variable | Intended owner |
|---|---|
| `flow.runId` | Start |
| `flow.currentStage` | workflow stage transitions / designated nodes |
| `flow.fileIntake` | discovery/intake layer |
| `flow.criteria` | Criteria Specialist / Master integration |
| `flow.suppliers` | Supplier Specialist / Master integration |
| `flow.knowledgeContext` | knowledge retrieval layer |
| `flow.clarificationPackage` | Master |
| `flow.evaluationConfiguration` | Configuration Validation after human confirmation |
| `flow.confirmationStatus` | Configuration Validation |
| `flow.confirmationNotes` | Configuration Validation |
| `flow.validationResult` | Questionnaire Validation |
| `flow.canonicalQuestionMap` | Canonical Mapping |
| `flow.scoringResult` | Evaluation Specialist / Master integration |
| `flow.evaluationQC` | Master |
| `flow.knockoutResult` | Knockout Evaluation Script |
| `flow.scoreValidationResult` | Score Validation Script |
| `flow.weightedScores` | Weighted Score Script |
| `flow.rankingResult` | Ranking Script |
| `flow.evaluationResult` | Result Builder |
| `flow.report` | Report-generation layer / export integration |
| `flow.reportStatus` | reporting layer |

The single-producer principle should be preserved wherever practical.

## 7. Handoff Architecture

Handoffs are configured through dedicated tools in the calling Agent. The observed implementation uses explicit parameter contracts rather than relying only on graph-edge context transfer.

### Criteria Handoff

Target: `criteria_deep_agent`

Parameters:

- `task`: string, dynamic
- `source_files`: array<object>, dynamic
- `known_context`: object, optional, dynamic
- `constraints`: array<string>, optional, dynamic
- `expected_output`: string, fixed

Return edge:

- Pass context: ON
- Transfer type: Last message (append)

### Supplier Handoff

Target: `supplier_deep_agent`

Parameters:

- `task`: string, dynamic
- `source_files`: array<object>, dynamic
- `known_context`: object, optional, dynamic
- `constraints`: array<string>, optional, dynamic
- `expected_output`: string, fixed

Return edge:

- Pass context: ON
- Transfer type: Last message (append)

### Evaluation Handoff

Target: `evaluation_deep_agent`

Parameters:

- `task`: string, dynamic
- `evaluation_configuration`: object, dynamic
- `canonical_evaluation_model`: object, dynamic, supplied from `flow.canonicalQuestionMap`
- `supplier_evidence`: object, dynamic, supplied from `flow.suppliers`
- `knowledge_context`: object, optional, dynamic
- `constraints`: array<string>, optional, dynamic
- `expected_output`: string, fixed

Return edge:

- Pass context: ON
- Transfer type: Last message (append)

### Execution & Reporting Handoff

Target: `execution_and_reporting_agent`

Parameters:

- `task`: string, dynamic
- `evaluation_configuration`: object, dynamic
- `canonical_evaluation_model`: object, dynamic, supplied from `flow.canonicalQuestionMap`
- `supplier_evidence`: object, dynamic, supplied from `flow.suppliers`
- `semantic_evaluation`: object, dynamic, supplied from `flow.scoringResult`
- `evaluation_qc`: object, dynamic, supplied from `flow.evaluationQC`
- `expected_output`: string, fixed

Execution is gated on:

```text
flow.evaluationQC.status = "PASS"
and
flow.evaluationQC.readyForExecution = true
```

### Knockout Handoff

Target: `knockout_evaluation`

The deterministic handoff should pass only the approved configuration and canonical execution structure required by the Script. Do not duplicate semantic evaluation when the canonical structure already contains the supplier response/evidence needed for knockout execution.

## 8. Context Management Baseline

For Deep Agents, the working baseline currently used is:

- Context Management: ON
- Scope: Tool results only
- Strategy: Replace
- Threshold unit: Tokens
- Long-term memory: OFF
- Error handling: OFF during initial build/debugging
- Include Thoughts: OFF
- Response Format: JSON for inter-stage contracts

Exact runtime semantics remain subject to controlled testing.

## 9. Human Confirmation Gate

Human Input sits after Master discovery and before configuration validation.

The Human Input node captures the response in `system.humanInput`.

The Human Input node does not directly write `flow.evaluationConfiguration`.

The intended sequence is:

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

The first implementation supports an explicit `APPROVE`, `CORRECT`, or `REJECT` response. Free-text correction application remains an area for later enhancement/verification; the current deterministic baseline does not silently interpret arbitrary corrections.

## 10. Canonical Mapping Contract

Canonical Mapping creates the normalized question-by-supplier structure used by downstream evaluation and deterministic processing.

Conceptually:

```text
criteria question
      ×
supplier response
      ×
approved configuration
      ↓
canonical question-supplier record
```

Each record contains, where available:

- question ID
- question number
- section
- question text
- supplier ID/name
- supplier answer
- answered flag
- mapping confidence
- approved weight
- guidance
- scoring rubric
- knockout confirmation
- acceptance condition
- criteria source
- supplier source

This follows the canonical Question Map contract already defined in the Bid Analysis Agent specification.

## 11. Deterministic Script Responsibilities

### Knockout Evaluation

Inputs:

- `evaluationConfiguration`
- `canonicalQuestionMap`
- `runId`

Output/state:

- `flow.knockoutResult`

Allowed statuses:

- `PASS`
- `FAIL`
- `AMBIGUOUS`
- `NOT_APPLICABLE`

Only confirmed knockout rules are executed.

### Score Validation

Inputs:

- `evaluationConfiguration`
- `canonicalQuestionMap`
- `scoringResult`
- `knockoutResult`

Output/state:

- `flow.scoreValidationResult`

Responsibilities:

- validate score range
- validate question membership
- preserve evidence/rationale
- calculate raw/section scores
- surface invalid score items

Does not apply weights.

### Weighted Score

Inputs:

- `evaluationConfiguration`
- `scoreValidationResult`
- `knockoutResult`
- `runId`

Output/state:

- `flow.weightedScores`

Responsibilities:

- apply approved weights
- calculate weighted contributions
- preserve qualification status

### Ranking

Inputs:

- `weightedScores`
- `knockoutResult`
- `runId`

Output/state:

- `flow.rankingResult`

Responsibilities:

- rank qualified suppliers by deterministic weighted score
- keep disqualified suppliers separately
- ensure disqualified suppliers do not receive qualified rank
- preserve deterministic tie handling

### Result Builder

Inputs:

- `runId`
- `evaluationConfiguration`
- `canonicalQuestionMap`
- `scoringResult`
- `knockoutResult`
- `scoreValidationResult`
- `weightedScores`
- `rankingResult`

Output/state:

- `flow.evaluationResult`

The Result Builder assembles the final authoritative evaluation result without changing the underlying results.

## 12. Report Generation Architecture

Report generation is owned by the `bid-analysis-report-generator` Skill attached to the Execution & Reporting Deep Agent.

The Skill bundles `Export Excel V2`.

Architecture:

```text
flow.evaluationResult
        ↓
Execution & Reporting Agent
        ↓
bid-analysis-report-generator Skill
        ↓
Export Excel V2
        ↓
flow.report / report artifact
        ↓
OUTPUT
```

The report is a presentation layer, not a second evaluation engine.

## 13. Report Template Baseline

The report is based on the supplied reference workbook for presentation and writing style.

Required sheets, in order:

1. `Executive Summary`
2. `Supplier Profiles`
3. `Q&A Scorecard`
4. `Score Legend`

Supplier count is dynamic. The report structure is fixed.

The Skill uses:

- Arial typography
- dark navy structural bands
- medium blue section bands
- pale blue supporting areas
- green/yellow/orange/red score semantics
- concise procurement-consulting language
- evidence-led supplier commentary
- no merge-cell functionality

## 14. Current Design Decisions

The following design choices are treated as the working baseline:

1. Use Handoff tools for specialist delegation.
2. Use explicit Handoff parameter contracts.
3. Use same Master Agent node for multiple workflow phases.
4. Use Flow Variables for shared business state.
5. Keep `system.humanInput` as immediate HITL capture.
6. Build authoritative configuration only after Human Input through Configuration Validation.
7. Canonicalize before semantic evaluation.
8. Keep semantic scoring separate from deterministic arithmetic/weighting/ranking.
9. Use a dedicated Execution & Reporting Agent for downstream orchestration.
10. Use a dedicated report-generation Skill with bundled Export Excel V2.
11. Keep report formatting knowledge in the Skill rather than the Execution & Reporting system prompt.
12. Do not use merge cells in the Excel report.

## 15. Verification Boundary

The architecture above is a design baseline informed by the current UI observations and the supplied Bid Analysis Agent contracts. It is not yet full runtime proof.

Runtime tests still need to establish:

- multi-stage re-entry behavior of the same Master Agent
- exact Handoff parameter serialization
- returned structured output shape across Handoffs
- Master structured-output field reference semantics
- state update transactionality and timing
- Human Input resume/branch behavior for CORRECT and REJECT
- deterministic Script execution with realistic source data
- Export Excel V2 execution and artifact persistence
- Skill invocation behavior and bundled-tool execution
- final Output artifact exposure

These unresolved items belong in Evidence/Verification once tested.
