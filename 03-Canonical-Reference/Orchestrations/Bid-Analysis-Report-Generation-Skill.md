# Bid Analysis Report Generation Skill

> **Status:** Design baseline + observed Skill configuration surface; runtime Skill/tool execution remains open.

## Purpose

The Bid Analysis Agent uses a dedicated Skill as the report-generation specialist. The Skill owns stable report-generation expertise, while `Export Excel V2` remains the workbook-rendering tool.

The separation is:

```text
Execution & Reporting Deep Agent
        ↓
Bid Analysis Report Generator Skill
        ↓
Export Excel V2
        ↓
XLSX artifact
```

The Skill should not become a second evaluation engine.

## Skill responsibilities

The Skill owns:

- report structure
- report writing style
- procurement-consulting tone
- workbook visual language
- supplier-dependent expansion logic
- score/status presentation
- report formatting rules
- sheet construction
- filename construction
- Export Excel V2 invocation

The Skill consumes authoritative workflow state rather than independently recomputing evaluation results.

## Required workbook structure

Every report contains exactly four sheets, in this order:

1. `Executive Summary`
2. `Supplier Profiles`
3. `Q&A Scorecard`
4. `Score Legend`

The number of suppliers is dynamic; the sheet system is fixed.

## Reference workbook

A supplied workbook is used only as a presentation reference. Sample factual data, supplier names, scores and numbers are not reused.

The intended report language is:

- concise
- evidence-led
- procurement-specific
- commercially useful
- comparative where relevant
- explicit about evidence gaps and uncertainty

The report should not read like generic AI prose.

## Visual language

Primary design colors:

| Use | Color |
|---|---|
| Primary structural navy | `#1F3864` |
| Secondary section blue | `#2E5EA6` |
| Pale blue grouping | `#EBF3FC` |
| Light blue grouping | `#D6E4F7` |
| Positive green | `#C6EFCE` with dark green text `#1E7B45` |
| Moderate yellow | `#FFEB9C` with dark gold text `#7B5E00` |
| Weak orange | `#FFE0B2` with orange text `#C75000` |
| Poor/critical red | `#FFC7CE` with dark red text `#C00000` |
| Rank 1 gold | `#FFD700` |
| Rank 2 dark grey | `#595959` |
| Rank 3 bronze | `#CD7F32` |
| Critical risk band | `#8B0000` |
| Border grey | `#CCCCCC` |

Typography baseline:

- Arial.
- 9–10 pt body text.
- 14–16 pt major workbook title.
- 10–12 pt section headers.
- Bold, white text on navy/blue structural bands.
- Wrapped narrative cells.
- Thin grey borders.

## Merge-cell constraint

The generated report deliberately does not use merge cells.

Where the reference workbook uses merged visual regions, reproduce the hierarchy using filled rows, repeated section fills, alignment, borders and explicit column placement.

Do not depend on merged cells.

## Score color semantics

For a 0–5 scoring scale:

- 5/4 → green.
- 3 → yellow.
- 2 → orange.
- 1/0 → red.

The score colors communicate evaluation status, not decoration.

## Sheet responsibilities

### Executive Summary

Provide the senior procurement view:

- supplier ranking
- overall score
- question completion
- critical summary
- section score comparison
- market-wide risk flags where material

Use concise, evidence-backed executive language.

### Supplier Profiles

Provide one compact executive profile per supplier:

- rank and overall score
- summary
- strengths
- weaknesses
- section scores and weights

Strengths use green visual treatment. Weaknesses use red visual treatment.

### Q&A Scorecard

Preserve question-level traceability.

Columns begin with:

```text
Q ID | Question
```

Every supplier then receives a four-column block:

```text
Supplier | Response | Score | Evaluator Comment
```

Supplier blocks expand horizontally with supplier count.

Questions and supplier answers remain source-faithful.

### Score Legend

Present the approved scoring methodology actually used in the run:

- score
- rating
- rubric/criteria
- methodology notes

Do not invent score meanings.

## Authority model

Primary report source:

`flow.evaluationResult`

Methodology:

`flow.evaluationConfiguration`

Question/source mapping:

`flow.canonicalQuestionMap`

Semantic evidence and comments:

`flow.scoringResult`

Deterministic qualification and calculations:

- `flow.knockoutResult`
- `flow.scoreValidationResult`
- `flow.weightedScores`
- `flow.rankingResult`

The Skill presents these outputs. It does not override them.

## Dynamic supplier scaling

### Executive Summary

Add one ranking row per supplier and one supplier column per section score.

### Supplier Profiles

Repeat the fixed supplier profile block once per supplier.

### Q&A Scorecard

Add one four-column supplier block per supplier.

### Score Legend

Remains fixed.

## Export Excel V2

The bundled renderer requires:

- `filename`
- `sheets`

Each sheet requires at minimum:

```text
name
 data
```

The tool also exposes headers, freeze panes, autofilter, tab color, column widths, header formatting, cell formats, conditional formatting and charts.

The Skill should construct the four-sheet payload dynamically and invoke the bundled tool after validating that the final evaluation state is available.

## Artifact/state design

The report artifact belongs to reporting state, not input file state.

The intended design is:

```text
Export Excel result
      ↓
flow.report
```

However, the exact runtime object returned by Export Excel V2 and the best artifact persistence path remain open until runtime testing confirms them.

## Invocation rule

Invoke this Skill only after deterministic evaluation has produced a usable `flow.evaluationResult`.

Do not generate a final report from incomplete semantic or deterministic state.

## Non-negotiable rules

Never:

- invent supplier claims
- invent missing attachments
- change approved weights
- change scoring scales
- change knockout rules
- override deterministic rankings
- conceal missing evidence
- copy sample workbook facts
- add unrequested sheets
- rename fixed sheets
- use merge cells
- turn the workbook into an AI essay

## Runtime boundary

The following are still unverified until controlled tests are executed:

- Skill invocation selection.
- Skill access to Flow Variables.
- Bundled-tool invocation from Skill.
- Export artifact persistence.
- Exact output object shape.
- Dynamic formatting fidelity.
- End-to-end Excel download/attachment behavior.
