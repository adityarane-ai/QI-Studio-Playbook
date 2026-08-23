# Bid Analysis Agent Node-by-Node Configuration

> **Status:** Working implementation reference.
> **Evidence status:** Design + observed QI Studio configuration; runtime semantics remain open unless separately confirmed.
> **Purpose:** Practical configuration reference for the Bid Analysis Agent currently being built in QI Studio.
>
> This document captures the configuration decisions made during implementation. It is intentionally separate from runtime evidence. Where an expression or setting is based on observed UI behavior but has not yet been runtime-tested, treat it as a working baseline rather than a platform guarantee.

## 1. Locked node sequence

```text
START
  ↓
MASTER DEEP AGENT
  ├── Handoff → CRITERIA DEEP AGENT → RETURN
  ├── Handoff → SUPPLIER DEEP AGENT → RETURN
  ↓
HUMAN INPUT
  ↓
CONFIGURATION VALIDATION
  ↓
QUESTIONNAIRE VALIDATION
  ↓
CANONICAL MAPPING
  ↓
MASTER DEEP AGENT (same node re-entered)
  └── Handoff → EVALUATION DEEP AGENT → RETURN
       ↓
     MASTER QC
       ↓
  Handoff → EXECUTION & REPORTING DEEP AGENT
       ↓
  Handoff → KNOCKOUT EVALUATION
       ↓
  SCORE VALIDATION
       ↓
  WEIGHTED SCORE
       ↓
  RANKING
       ↓
  RESULT BUILDER
       ↓
  REPORT GENERATION SKILL
       ↓
  EXPORT EXCEL V2
       ↓
  OUTPUT
```

## 2. Global Agent baseline

The current Deep Agent baseline used across the specialist/master agents is:

| Setting | Baseline |
|---|---|
| Agent Strategy | `Deep Agent` |
| Model | Claude Opus 4.6 in the current build |
| Response Format | JSON for inter-stage contracts |
| Include Thoughts | OFF |
| Long-term Memory | OFF |
| Error Handling | OFF during initial construction |
| Context Management | ON |
| Context Scope | Tool results only |
| Context Strategy | Replace |
| Threshold | Tokens |
| Return edge baseline | Pass context ON + Last message (append) |

These are implementation choices, not universal QI Studio defaults.

## 3. START

### Inputs

| Input | Type | Required |
|---|---|---:|
| `message` | string | Yes |
| `attachments` | array | No |
| `ui_action` | object | No |

### State updates

The current build establishes:

```text
system/userQuery        ← message
system/attachments      ← attachments
system/uiAction         ← ui_action
flow/runId              ← system/sessionId
flow/currentStage       ← "intake"
```

Other platform-managed values remain under their native System scope.

### Design rule

Start performs intake/state initialization. It does not perform bid analysis.

---

## 4. MASTER DEEP AGENT

The same Master node is deliberately re-entered after Canonical Mapping. It therefore uses phase-aware instructions rather than separate Master nodes.

### Stable User Message

The Master user message is designed to expose the current workflow state and ask the Agent to manage the current phase. The current pattern is:

```text
Manage the current phase of the RFP qualitative bid analysis workflow.

User Request:
{{system.userQuery}}

Run ID:
{{flow.runId}}

Current Stage:
{{flow.currentStage}}

Available Attachments:
{{system.attachments}}

Current Criteria:
{{flow.criteria}}

Current Supplier Evidence:
{{flow.suppliers}}

Current Knowledge Context:
{{flow.knowledgeContext}}

Clarification Package:
{{flow.clarificationPackage}}

Evaluation Configuration:
{{flow.evaluationConfiguration}}

Validation Result:
{{flow.validationResult}}

Canonical Evaluation Model:
{{flow.canonicalQuestionMap}}

Semantic Evaluation:
{{flow.scoringResult}}

Evaluation QC:
{{flow.evaluationQC}}

Final Evaluation Result:
{{flow.evaluationResult}}

Determine the appropriate action for the current workflow phase.

Do not repeat completed work.

Use the designated Handoff tool for specialist work.

Maintain the authority boundaries defined in the system instructions.
```

### Phase behavior

The Master interprets `flow.currentStage`.

**Discovery / clarification:** delegate Criteria and Supplier work, integrate returns, identify ambiguity, construct `flow.clarificationPackage`.

**Validation / canonicalization:** consume the validated configuration and canonical map without repeating discovery.

**Evaluation:** hand off to Evaluation Deep Agent, challenge the semantic result, produce `flow.evaluationQC`, and only then hand off to Execution & Reporting.

### Tools

The Master currently uses:

- `handoff_to_criteria_agent`
- `handoff_to_supplier_agent`
- `handoff_to_evaluation_agent`
- `handoff_to_execution_and_reporting_agent`

Knowledge tools/library may also be attached where required by the knowledge workflow.

---

## 5. HANDOFF TOOL: CRITERIA

### Name

`handoff_to_criteria_agent`

### Target

`criteria_deep_agent`

### Description baseline

```text
Delegate RFP and evaluation-framework analysis to the Criteria Deep Agent. Provide the relevant task, source files, known context, constraints, and expected structured output.
```

### Parameters

```text
`task`               string
`source_files`       array<object>
`known_context`      object
`constraints`        array<string>
`expected_output`   string
```

`task`, `source_files`, `known_context`, and `constraints` are dynamic/Agent-decided. `expected_output` is fixed.

### Fixed expected output

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

### Return edge

```text
Pass context: ON
Transfer type: Last message (append)
```

---

## 6. CRITERIA DEEP AGENT

### Stable user role

The agent's user message carries the general task and user query, while Handoff parameters provide the invocation-specific task, files, context and constraints.

### Responsibilities

- understand the RFP/evaluation framework
- identify sections, criteria and questions
- identify scoring scale/rubric
- identify weights
- identify mandatory requirements
- identify candidate knockout conditions
- identify ambiguities and missing information
- preserve provenance

It does not own final approval, final knockout execution, weighted scoring, or ranking.

---

## 7. HANDOFF TOOL: SUPPLIER

### Name

`handoff_to_supplier_agent`

### Target

`supplier_deep_agent`

### Description baseline

```text
Delegate supplier response and evidence analysis to the Supplier Deep Agent. Provide the relevant supplier files, task, known context, source-first constraints, and expected structured output.
```

### Parameters

```text
`task`               string
`source_files`       array<object>
`known_context`      object
`constraints`        array<string>
`expected_output`   string
```

### Fixed expected output

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

### Return edge

```text
Pass context: ON
Transfer type: Last message (append)
```

---

## 8. SUPPLIER DEEP AGENT

### Responsibilities

- identify suppliers
- preserve supplier response content
- map responses to questions
- preserve source references
- identify missing/ambiguous responses
- identify supplier exceptions/deviations
- identify evidence gaps

It must never invent a supplier claim or fill missing source information from external knowledge.

---

## 9. HUMAN INPUT

### Question

Current working prompt:

```text
Review the proposed bid evaluation configuration below.

{{flow.clarificationPackage}}

Please review the proposed evaluation criteria, scoring methodology, weights, mandatory requirements, and candidate knockout conditions.

Reply with one of:

APPROVE
Approve the proposed configuration.

CORRECT
Provide the specific corrections required.

REJECT
Reject the proposed configuration and explain what is incorrect or missing.
```

### Save Response As

```text
system.humanInput
```

### State Update

None. The immediate human response is captured by the native Human Input mechanism; Configuration Validation becomes the producer of the authoritative configuration state.

### Intended transition

```text
flow.clarificationPackage
        +
system.humanInput
        ↓
Configuration Validation
```

### Current limitation

The first implementation deliberately avoids silently applying arbitrary free-text `CORRECT` instructions. The correction path requires explicit runtime design/testing for safe parsing and resume behavior.

---

## 10. CONFIGURATION VALIDATION

### Description

```text
Validate the human confirmation response and create the authoritative evaluation configuration for the current run.
```

### Inputs

```text
clarificationPackage       object
humanInput                 object
runId                      string
currentStage               string
```

### Input mappings

```text
clarificationPackage  → {{flow.clarificationPackage}}
humanInput            → {{system.humanInput}}
runId                 → {{flow.runId}}
currentStage          → {{flow.currentStage}}
```

### Outputs

```text
validationStatus
confirmationStatus
evaluationConfiguration
confirmationNotes
errors
warnings
runId
```

### State updates

```text
flow.evaluationConfiguration
    Set
    {{nodes.configuration_validation.result.evaluationConfiguration}}

flow.confirmationStatus
    Set
    {{nodes.configuration_validation.result.confirmationStatus}}

flow.confirmationNotes
    Set
    {{nodes.configuration_validation.result.confirmationNotes}}
```

### Current working behavior

- `APPROVE` creates an approved configuration from the Master proposal.
- `CORRECT` returns `correction_required` rather than guessing free-text changes.
- `REJECT` returns `rejected`.
- unknown responses return `invalid_response`.

### Ownership

`flow.evaluationConfiguration` is produced here, not by Human Input itself.

---

## 11. QUESTIONNAIRE VALIDATION

### Description

```text
Validate criteria, supplier responses, question mappings, and approved evaluation configuration before canonicalization.
```

### Inputs

```text
criteria                  object
suppliers                 object
evaluationConfiguration  object
runId                     string
```

### Input mappings

```text
criteria                 → {{flow.criteria}}
suppliers                → {{flow.suppliers}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
runId                    → {{flow.runId}}
```

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
    Set
    {{nodes.questionnaire_validation.result}}
```

### Responsibility

This is a structural validation boundary. It does not evaluate suppliers, calculate scores, apply weights, or execute knockouts.

---

## 12. CANONICAL MAPPING

### Description

```text
Build the canonical question map by combining validated criteria, supplier responses, and the approved evaluation configuration.
```

### Inputs

```text
criteria                  object
suppliers                 object
evaluationConfiguration  object
validationResult         object
runId                    string
```

### Input mappings

```text
criteria                 → {{flow.criteria}}
suppliers                → {{flow.suppliers}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
validationResult         → {{flow.validationResult}}
runId                    → {{flow.runId}}
```

### Output

```text
questions : array<object>
```

### State update

```text
flow.canonicalQuestionMap
    Set
    {{nodes.canonical_mapping.result}}
```

### Stage transition

Upon successful canonicalization:

```text
flow.currentStage
    Set
    evaluation
```

### Canonical record pattern

Each question-supplier record contains, where available:

- question ID
- question number
- section name
- question text
- supplier ID/name
- supplier answer
- answered flag
- mapping confidence
- approved weight
- scoring guidance/rubric
- confirmed knockout flag
- acceptance condition
- criteria source
- supplier source

---

## 13. EVALUATION HANDOFF

### Name

`handoff_to_evaluation_agent`

### Target

`evaluation_deep_agent`

### Parameters

```text
`task`                       string
`evaluation_configuration`  object
`canonical_evaluation_model` object
`supplier_evidence`         object
`knowledge_context`         object
`constraints`               array<string>
`expected_output`           string
```

### Mappings

```text
evaluation_configuration  → {{flow.evaluationConfiguration}}
canonical_evaluation_model → {{flow.canonicalQuestionMap}}
supplier_evidence         → {{flow.suppliers}}
knowledge_context         → {{flow.knowledgeContext}}
```

The Master dynamically decides `task` and `constraints`.

### Fixed expected output

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

### Return edge

```text
Pass context: ON
Transfer type: Last message (append)
```

---

## 14. MASTER EVALUATION QC

After Evaluation Deep Agent returns, the same Master validates the semantic result.

### QC dimensions

- coverage
- evidence sufficiency
- rubric alignment
- cross-supplier consistency
- adherence to approved configuration
- unsupported claims or fabricated evidence
- material omissions or contradictions

### Intended QC object

```json
{
  "status": "PASS | FAIL",
  "readyForExecution": true,
  "checks": [],
  "issues": [],
  "summary": ""
}
```

### State owner

```text
flow.evaluationQC
```

### Execution gate

The Execution & Reporting Handoff should only be invoked when:

```text
flow.evaluationQC.status = "PASS"
```

and

```text
flow.evaluationQC.readyForExecution = true
```

> **Runtime verification note:** The exact field-path mapping from the Agent's `structuredOutput` to `flow.evaluationQC` remains a verification item until tested. The intended expression pattern is `{{nodes.master_deep_agent.structuredOutput.evaluationQC}}`.

---

## 15. EXECUTION & REPORTING DEEP AGENT

### Role

Orchestrate deterministic execution and invoke the report-generation Skill after the final deterministic result exists.

It is not itself the scoring engine or report-formatting engine.

### Stable user message

```text
Execute the approved bid evaluation and complete the deterministic execution and reporting phase.

CURRENT RUN

Run ID:
{{flow.runId}}

Current Stage:
{{flow.currentStage}}

APPROVED EVALUATION CONFIGURATION

{{flow.evaluationConfiguration}}

CANONICAL EVALUATION MODEL

{{flow.canonicalEvaluationModel}}

SEMANTIC EVALUATION

{{flow.scoringResult}}

SUPPLIER EVIDENCE

{{flow.suppliers}}

EVALUATION QC

{{flow.evaluationQC}}

CURRENT DETERMINISTIC STATE

Knockout Result:
{{flow.knockoutResult}}

Score Validation Result:
{{flow.scoreValidationResult}}

Weighted Scores:
{{flow.weightedScores}}

Ranking Result:
{{flow.rankingResult}}

Final Evaluation Result:
{{flow.evaluationResult}}

EXECUTION OBJECTIVE

1. Start or continue deterministic execution using the designated knockout handoff.
2. Allow the downstream deterministic processing chain to complete.
3. Treat deterministic outputs as authoritative.
4. When flow.evaluationResult is available and valid, invoke the bid-analysis-report-generator skill.
5. Return the final execution and reporting status.

Do not modify the approved evaluation configuration.
Do not independently calculate or override deterministic results.
Do not construct the Excel report directly. Use the report-generation skill.
```

### State updates

```text
flow.currentStage = "execution"
flow.reportStatus = "pending"
```

Do not make this Agent the producer of `flow.knockoutResult`, `flow.scoreValidationResult`, `flow.weightedScores`, `flow.rankingResult`, or `flow.evaluationResult`.

### Capabilities

Preferred current design:

```text
Tool:
  handoff_to_knockout_evaluation

Skill:
  bid-analysis-report-generator
```

`Export Excel V2` is bundled in the Skill rather than directly attached to the Agent.

---

## 16. EXECUTION HANDOFF

### Tool

`handoff_to_execution_and_reporting_agent`

### Target

`execution_and_reporting_agent`

### Parameters

```text
`task`                       string
`evaluation_configuration`  object
`canonical_evaluation_model` object
`supplier_evidence`         object
`semantic_evaluation`       object
evaluation_qc`              object
`expected_output`            string
```

### Mappings

```text
evaluation_configuration  → {{flow.evaluationConfiguration}}
canonical_evaluation_model → {{flow.canonicalQuestionMap}}
supplier_evidence         → {{flow.suppliers}}
semantic_evaluation       → {{flow.scoringResult}}
evaluation_qc             → {{flow.evaluationQC}}
```

### Fixed expected output

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

---

## 17. KNOCKOUT EVALUATION SCRIPT

### Description

```text
Execute confirmed knockout rules against the canonical supplier evaluation data and return deterministic qualification results.
```

### Inputs

```text
evaluationConfiguration  object
canonicalQuestionMap      object
runId                     string
```

### Input mappings

```text
evaluationConfiguration → {{flow.evaluationConfiguration}}
canonicalQuestionMap     → {{flow.canonicalQuestionMap}}
runId                   → {{flow.runId}}
```

### Output

```text
suppliers : array<object>
```

Supplier result pattern:

```json
{
  "supplierId": "...",
  "supplierName": "...",
  "qualified": true,
  "status": "PASS",
  "failedRules": [],
  "ambiguousRules": [],
  "decisions": []
}
```

### State update

```text
flow.knockoutResult
    Set
    {{nodes.knockout_evaluation.result}}
```

### Design rule

Execute only confirmed knockout rules. Do not calculate score, weighting or ranking. Structured acceptance conditions should be evaluated deterministically; unsupported free-text rule interpretation should produce an ambiguity rather than an invented decision.

---

## 18. SCORE VALIDATION SCRIPT

### Description

```text
Validate semantic score recommendations against the approved evaluation configuration and produce deterministic validated score results.
```

### Inputs

```text
evaluationConfiguration  object
canonicalQuestionMap      object
scoringResult             object
knockoutResult            object
```

### Input mappings

```text
evaluationConfiguration → {{flow.evaluationConfiguration}}
canonicalQuestionMap     → {{flow.canonicalQuestionMap}}
scoringResult            → {{flow.scoringResult}}
knockoutResult           → {{flow.knockoutResult}}
```

### Output

```text
validationStatus  string
scoreRange        object
suppliers         array<object>
errors            array<object>
```

### State update

```text
flow.scoreValidationResult
    Set
    {{nodes.score_validation.result}}
```

### Design rule

Validate score range, question membership, evidence/rationale preservation and raw section calculations. Do not apply approved weights here. Do not rank suppliers.

---

## 19. WEIGHTED SCORE SCRIPT

### Description

```text
Apply the human-approved evaluation weights to validated supplier scores and produce deterministic weighted results.
```

### Inputs

```text
evaluationConfiguration  object
scoreValidationResult     object
knockoutResult            object
runId                     string
```

### Input mappings

```text
evaluationConfiguration → {{flow.evaluationConfiguration}}
scoreValidationResult   → {{flow.scoreValidationResult}}
knockoutResult           → {{flow.knockoutResult}}
runId                   → {{flow.runId}}
```

### Outputs

```text
runId
calculationStatus
totalApprovedWeight
suppliers
```

### State update

```text
flow.weightedScores
    Set
    {{nodes.weighted_score.result}}
```

### Design rule

Use only the approved weights. A properly configured set of percentage weights should sum to 100. The node converts validated section achievement into weighted contribution and does not rank suppliers.

---

## 20. RANKING SCRIPT

### Description

```text
Deterministically rank qualified suppliers using the validated weighted evaluation results and confirmed qualification status.
```

### Inputs

```text
weightedScores   object
knockoutResult   object
runId            string
```

### Input mappings

```text
weightedScores → {{flow.weightedScores}}
knockoutResult → {{flow.knockoutResult}}
runId          → {{flow.runId}}
```

### Outputs

```text
runId
decided rankings
disqualifiedSuppliers
tieHandling
```

Canonical implementation output names used in the build:

```text
runId
rankings
 disqualifiedSuppliers
tieHandling
```

### State update

```text
flow.rankingResult
    Set
    {{nodes.ranking.result}}
```

### Design rule

Rank only suppliers that passed deterministic qualification and have valid weighted scores. Disqualified or ambiguous suppliers receive no qualified rank. Ties use standard competition ranking in the current implementation.

---

## 21. RESULT BUILDER SCRIPT

### Description

```text
Assemble the authoritative deterministic bid evaluation result from the approved configuration, semantic evaluation, qualification, validated scores, weighted scores, and ranking.
```

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

### Input mappings

```text
runId                    → {{flow.runId}}
evaluationConfiguration → {{flow.evaluationConfiguration}}
canonicalQuestionMap     → {{flow.canonicalQuestionMap}}
scoringResult            → {{flow.scoringResult}}
knockoutResult           → {{flow.knockoutResult}}
scoreValidationResult    → {{flow.scoreValidationResult}}
weightedScores           → {{flow.weightedScores}}
rankingResult            → {{flow.rankingResult}}
```

### State update

```text
flow.evaluationResult
    Set
    {{nodes.result_builder.result}}
```

### Design rule

Result Builder assembles. It does not recalculate qualification, scores, weights, or ranking.

---

## 22. REPORT GENERATION SKILL

### Skill name

`bid-analysis-report-generator`

### Purpose

Generate the final consultant-ready Excel report from authoritative workflow state.

### Bundled tool

`Export Excel V2`

### Report contract

Exactly four sheets, in this order:

1. `Executive Summary`
2. `Supplier Profiles`
3. `Q&A Scorecard`
4. `Score Legend`

The supplied reference workbook is used for tone, writing style, visual hierarchy, and formatting logic. Sample values are not authoritative and must not be copied.

### Important presentation constraint

Do not use merged cells. Reproduce the visual effect using fills, typography, borders, alignment, row height, and repeated section-band cells.

### Palette baseline

| Use | Color |
|---|---|
| Primary navy | `#1F3864` |
| Medium blue | `#2E5EA6` |
| Pale blue | `#EBF3FC` |
| Light blue | `#D6E4F7` |
| Positive green fill | `#C6EFCE` |
| Positive green font | `#1E7B45` |
| Moderate yellow fill | `#FFEB9C` |
| Moderate yellow font | `#7B5E00` |
| Weak orange fill | `#FFE0B2` |
| Weak orange font | `#C75000` |
| Poor red fill | `#FFC7CE` |
| Poor red font | `#C00000` |
| Rank 1 | `#FFD700` |
| Rank 2 | `#595959` |
| Rank 3 | `#CD7F32` |
| Critical risk | `#8B0000` |
| Border grey | `#CCCCCC` |

Typography baseline: Arial, compact consulting-style sizing, wrapped narrative text, thin light-grey borders.

### Tool parameters

```text
filename : string
sheets   : array
```

Both are dynamic/Skill-decided.

The report Skill owns construction of the `sheets` payload. Export Excel V2 is the renderer.

### Tool state

The current implementation is intended to keep the generated report in `flow.report` and reporting state in `flow.reportStatus`, but artifact persistence and exact tool-result shape remain runtime verification items.

---

## 23. OUTPUT

### Description

```text
Final output of the completed bid analysis evaluation and generated Excel report.
```

### Response mapping baseline

```text
messages
→ {{nodes.execution_and_reporting_agent.text}}
```

Platform metadata remains:

```text
selectedAgentId   → runtime.workflowMetaData.workflowId
selectedAgentName → runtime.workflowMetaData.agentName
```

### Additional output variables

Intended report metadata:

```text
report           → {{flow.report}}
reportStatus     → {{flow.reportStatus}}
evaluationResult → {{flow.evaluationResult}}
```

### Current limitation

The exact artifact/result representation returned by Export Excel V2 and how it should be exposed through Output remain runtime verification items.

---

## 24. Expression Reference Convention

When a node must consume the entire result of a Script node, use:

```text
{{nodes.<nodeName>.result}}
```

Examples:

```text
{{nodes.knockout_evaluation.result}}
{{nodes.score_validation.result}}
{{nodes.weighted_score.result}}
{{nodes.ranking.result}}
{{nodes.result_builder.result}}
```

When a specific returned field is needed, use the field-specific form:

```text
{{nodes.configuration_validation.result.evaluationConfiguration}}
{{nodes.configuration_validation.result.confirmationStatus}}
{{nodes.configuration_validation.result.confirmationNotes}}
```

Always insert these through the QI Studio dynamic expression/token system where possible. A plain-text copy of an expression is not equivalent to a runtime reference.

---

## 25. State Ownership Table

| Flow variable | Producer |
|---|---|
| `flow.runId` | Start |
| `flow.currentStage` | stage transition logic |
| `flow.criteria` | Criteria/Discovery layer |
| `flow.suppliers` | Supplier/Discovery layer |
| `flow.knowledgeContext` | knowledge retrieval layer |
| `flow.clarificationPackage` | Master |
| `flow.evaluationConfiguration` | Configuration Validation |
| `flow.confirmationStatus` | Configuration Validation |
| `flow.confirmationNotes` | Configuration Validation |
| `flow.validationResult` | Questionnaire Validation |
| `flow.canonicalQuestionMap` | Canonical Mapping |
| `flow.scoringResult` | Evaluation Agent/Master integration |
| `flow.evaluationQC` | Master |
| `flow.knockoutResult` | Knockout Evaluation |
| `flow.scoreValidationResult` | Score Validation |
| `flow.weightedScores` | Weighted Score |
| `flow.rankingResult` | Ranking |
| `flow.evaluationResult` | Result Builder |
| `flow.report` | Report generation/export integration |
| `flow.reportStatus` | Reporting layer |

Preserve the single-producer principle wherever practical.

---

## 26. Runtime Verification Boundary

This document captures the working build, not proof of all runtime semantics.

Known open areas include:

- same-Agent multi-stage re-entry
- Handoff parameter serialization and return aggregation
- Human Input correction/rejection and resume behavior
- Agent structured-output field mapping
- Script nested object schema enforcement
- State Update transactionality/order
- Export Excel execution and artifact persistence
- Skill invocation and bundled-tool execution
- Output artifact exposure

When these are tested, record results in `04-Evidence/Runtime/`, update this document if the implementation baseline changes, and remove the resolved item from `05-Verification/` according to the Playbook lifecycle.
