# Evidence Record: Agent Node

**Evidence ID:** QI-OBS-2026-08-23-AGENT-001  
**Date:** 2026-08-23  
**Capability:** Agent strategy, model selection, prompt/message roles, tools/capabilities, advanced settings, context management, long-term memory, state updates, variable browser, outputs.  
**Source:** User-supplied QI Studio screenshots and supplied tool configuration text.

## What was observed

1. Agent strategy selection includes ReAct and Deep Agent in the observed UI.
2. Deep Agent exposes Enable SubAgents, a displayed maximum of three parallel subagents, and a parallel execution toggle.
3. Agent model selection exposes multiple provider families, including OpenAI, Google, and Anthropic.
4. The Agent message editor supports system, user, and assistant roles and variable insertion.
5. The Agent exposes Tools, Libraries, Skills, Widgets, and Connectors sections.
6. Advanced settings expose Response Format, Include Thoughts, Guardrails, Context Management, Long-term Memory, Error Handling, State Update, and Output Variables.
7. Response Format visibly offers Plain Text and JSON.
8. Context Management exposes Tool results only and Full history scopes.
9. Context Management exposes Replace and Drop strategies.
10. Context Management threshold can be configured using Tokens or Turns.
11. Long-term Memory is a separate toggle and is described as persisting useful facts across sessions.
12. State Update exposes Append, Extend, Set, and Clear in the observed Agent UI.
13. Conversation-history state updates expose User, Assistant, System, and Tool roles.
14. The variable browser groups values by scopes including Current Node, workflow/referenced contexts, Environment, Authorization, Metadata, Flow, and System.
15. The variable browser visibly includes system values such as attachments, files, humanInput, sessionId, timestamp, uiAction, and userQuery.
16. The variable browser visibly includes environment/authentication/metadata values such as baseURL, Authorization, RuntimeToken, agentName, modelId, version, and workflowId.
17. Flow/business objects visible in the browser include evaluationCriteria, knockoutResult, scoringResult, supplierSubmission, and validationResult.
18. Agent output variables visibly include text, toolCalls, structuredOutput, success, error, error.message, and error.status_code.

## What this evidence proves

It proves that these configuration controls, scopes, values, and output fields are present in the observed QI Studio build/UI, plus the tool contracts documented below as supplied configuration evidence.

## What this evidence does not prove

Screenshots and supplied configuration descriptions do not establish exact runtime semantics for context replacement, dropping, thresholds, long-term-memory retrieval timing, error retries, guardrail sequencing, Include Thoughts payloads, transactionality of state updates, scoped-variable precedence, Deep Agent scheduling/failure semantics, or the precise runtime behavior of export formatting and charts.

## Security note

The screenshots show fields named Authorization and RuntimeToken in the variable browser. These are system-owned sensitive values. Real credentials must never be stored in the playbook.

## Follow-up experiments

- Compare Tool-results-only vs Full-history context management under identical prompts and large contexts.
- Compare Replace vs Drop behavior and inspect actual message payloads/tokens.
- Test threshold boundary conditions in Tokens and Turns modes.
- Enable/disable Long-term Memory and verify persistence across sessions.
- Test Agent state updates with Append, Extend, Set, Clear on arrays and objects.
- Trigger a failing Agent and document Error Handling behavior.
- Test structuredOutput vs text downstream consumption.
- Test Deep Agent with 0, 1, 2, and 3 subagents and observe scheduling/aggregation.
- Test partial subagent failure and parent behavior.
- Test variable resolution when same-named fields exist in multiple scopes.
- Determine exact runtime representation and exposure behavior for Include Thoughts.

## Agent Tool Evidence Captured

The following tool configurations were subsequently supplied as screenshots or explicit tool descriptions. The records below document only what the supplied evidence establishes and explicitly distinguish UI/configuration evidence from runtime assumptions.

### 1. Get Reference File

**Tool name:** `get_reference_file`

**Purpose:** Retrieves the textual content of a knowledge item using its unique identifier. The configured description states that the function queries a knowledge repository using the provided `knowledgeId` and returns the associated content.

**Output behavior shown:** Returns a string containing the textual content of the knowledge item, or an empty string when the item does not exist or has no content.

**Parameters observed:**

| Parameter | Type | Observed rule |
|---|---|---|
| `library_id` | string | Use only the ID returned by `get_library_metadata`; no modification, shortening, guessing, or recall from memory. Re-verify the ID against metadata character by character. |
| `knowledge_id` | string | Same exact-ID verification rule as `library_id`. |

**Configured operating note:** The tool should not be called repeatedly with the same `knowledgeId` unless the knowledge identifier is different; repeated calls with the same knowledge item are described as redundant processing/resource usage.

**Advanced settings observed:** Include Thoughts ON; Response Filtering = Exclude Fields; Store Tool Output OFF; Return Direct OFF; Human-in-the-Loop OFF.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact repository lookup failure semantics, whitespace/encoding behavior, maximum content size, and whether output truncation occurs have not been established.

### 2. Get Table Schema

**Tool name:** `get_table_schema`

**Purpose:** Retrieves the schema of a knowledge source and is explicitly intended to be called first to understand which fields are available before searching or filtering.

**When to use, as configured:**

- Before performing any search, to understand available fields.
- When field names are needed for filtering.
- To understand the data structure.

**Return structure shown:** A JSON array of field objects. Each field object contains:

- `columnName` - exact field name to use in filters, sorts, aggregations, and includes.
- `dataType` - field type such as TEXT, DOUBLE, INTEGER, DATE, BOOLEAN, etc.
- `description` - description of the field.
- `isVectorized` - `true` for TEXT-indexed/full-text-search suitability and `false` for keyword/numeric-only use.

**Workflow shown:**

```text
get_table_schema
      ↓
resolve_field_value   (when actual filter values are needed)
      ↓
search_table_data
```

**Parameters observed:**

| Parameter | Type | Observed rule |
|---|---|---|
| `library_id` | string | Exact ID from `get_library_metadata`; verify character by character. |
| `knowledge_id` | string | Exact ID from `get_library_metadata`; verify character by character. |

**Advanced settings observed:** Include Thoughts ON; Response Filtering = Exclude Fields; Store Tool Output OFF; Return Direct OFF; Human-in-the-Loop OFF.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact schema object ordering, field-type edge cases, behavior for missing/empty schemas, and whether schema results are cached have not been established.

### 3. Resolve Field Value

**Tool name:** `resolve_field_value`

**Purpose:** Resolves closer field values semantically or through synonyms so that a user's mentioned value can be converted into an exact value suitable for filtering against actual data.

**Important workflow rule shown:** Call `get_table_schema` first to obtain available fields.

**Use when:**

- A user mentions a specific value that needs exact matching, such as `Microsoft` in "orders from Microsoft".
- A user mentions a status/value that needs exact matching, such as `approved` in "approved invoices".
- The search text requires synonym matching or closest-value resolution.

**Do not use when:**

- Asking what/which/how many values exist.
- Listing or counting field values.
- Grouping or aggregating by a field.
- The query uses unique identifiers such as IDs, codes, or numbers.

**Simple test shown:** If the user mentioned a specific value to filter and the filter requires semantic or synonym matching, use the tool; otherwise skip it.

**Output shown:** Returns the top closer values semantically or by synonyms from actual data.

**Parameters observed:**

| Parameter | Type | Observed purpose |
|---|---|---|
| `library_id` | string | Exact library ID returned by `get_library_metadata`. |
| `knowledge_id` | string | Exact knowledge ID returned by `get_library_metadata`. |
| `fields` | string | Fields for which values should be resolved. |
| `search_hint` | string | Narrows the search to the entity/value context mentioned by the user. Examples shown include supplier `Acme Corp`, category `electronics`, and status `pending`. |
| `max_values_per_field` | integer | Maximum number of unique values returned per field; default shown as 50. |

**Advanced settings observed:** Include Thoughts ON; Response Filtering = Exclude Fields in the captured configuration. Additional lower-screen settings were not fully visible in the supplied screenshot and are therefore not inferred here.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact ranking/scoring logic, tie handling, confidence thresholds, behavior when no close match exists, and response shape have not been established.

### 4. Search Table Data

**Tool name:** `search_table_data`

**Purpose:** Universal search over structured knowledge data. The configured description states that it searches, filters, and aggregates data.

**Use directly for:**

- Listing values, such as what suppliers exist or all statuses.
- Counting, including `how many`, `count_of`, and `unique_count`.
- Statistics such as sum, average, minimum, and maximum.
- Trends such as monthly sales and yearly growth.
- Grouping such as group by country or breakdown by category.
- Document lookup such as an order number.
- Semantic document search such as finding documents about a topic.

**Use after discovered values:**

- When `discoveredValues` are supplied by a previous call.
- When a user mentions a specific filter value that requires synonym matching, such as "spend with Microsoft".

**Capabilities shown:**

- Filters: `In`, `Contains`, `Range`, `AND/OR`.
- Aggregations: `GroupBy`, `DateHistogram`, `Sum`, `Avg`, `Min`, `Max`, `Count`, `UniqueCount`.
- Search: Semantic, keyword, hybrid.

**Important configured workflow constraints:**

- Call `get_table_schema` first to get available fields.
- Use the schema's `columnName` values as field names in conditions and aggregations.
- Use discovered/resolved values where synonym-aware exact filtering is needed.

**Parameters observed:**

| Parameter | Type | Observed purpose |
|---|---|---|
| `library_id` | string | Exact ID from `get_library_metadata`. |
| `knowledge_id` | string | Exact ID from `get_library_metadata`. |
| `query` | string | Search query. The UI description references condition/search syntax and explicitly directs the agent to use schema field names. |
| `resolved_values` | string | JSON containing values resolved via `resolve_field_value`, used for exact filter matching. |
| `filter_fields` | string | Comma-separated schema field names to use for filtering. |
| `include_fields` | string | Comma-separated schema field names to include in the search response. The guidance says to specify this to optimize response size/performance and use all fields only rarely. |
| `page` | integer | 1-based result page; default shown as 1. |
| `size` | integer | Results per page; default shown as 10, maximum shown as 1000. Ignored for aggregate-only queries that return zero documents. |

**Advanced configuration:** The supplied screenshot establishes the tool parameters and operational guidance. The lower advanced-settings controls were not fully visible in the supplied capture, so they are not inferred here.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact query grammar, pagination consistency, aggregation response shape, interaction between semantic and keyword/hybrid modes, filter precedence, and behavior at the size limit remain to be tested.

### 5. Recall Memory

**Tool name:** `recall_memory`

**Purpose:** Recalls previously saved long-term memories relevant to the current request. The configured description says it semantically searches the user's/agent's saved facts and returns the closest matches, including `memoryId`, content, and relevance.

**Use when:**

- The user refers to something they told the agent earlier, such as "as I mentioned" or "my usual".
- Durable preferences or facts need to be known before answering or acting.

**Important configured rule:** Call `recall_memory` before saving a new memory to avoid duplicates. Do not pass identity or tenant keys because scope is applied automatically.

**Parameter observed:**

| Parameter | Type | Observed purpose |
|---|---|---|
| `query` | string | Natural-language description of what should be recalled. |

**Advanced settings observed:** Include Thoughts ON; Response Filtering = Exclude Fields; Store Tool Output OFF; Return Direct OFF; Human-in-the-Loop OFF.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact relevance/ranking behavior, memory scope, persistence timing, duplicate handling, and result object schema beyond the displayed description remain to be tested.

### 6. Save Memory

**Tool name:** `save_memory`

**Purpose:** Saves a concise, durable fact for use in future conversations. The configured description explicitly frames the memory as a stable user preference, decision, or key detail rather than transient chit-chat.

**Configured rules shown:**

- Save one atomic fact per call, phrased so it remains useful later.
- Recall first to avoid saving a duplicate.
- Never save secrets, credentials, or sensitive personal data.
- Scope is applied automatically by the system and should not be included in `content`.

**Parameter observed:**

| Parameter | Type | Observed purpose |
|---|---|---|
| `content` | string | The single, self-contained fact to remember. |

**Advanced settings observed:** Include Thoughts ON; Response Filtering = Exclude Fields; Store Tool Output OFF; Return Direct OFF; Human-in-the-Loop OFF.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact save result/return shape, duplicate detection behavior, persistence timing, normalization, maximum content size, and failure behavior remain to be tested.

### 7. Update Memory

**Tool name:** `update_memory`

**Purpose:** Updates the content of an existing memory identified by `memoryId`, replacing the stored fact while preserving its scope. The configured description says to use this instead of saving a near-duplicate when a known fact has changed.

**Parameters observed:**

| Parameter | Type | Observed purpose |
|---|---|---|
| `memoryId` | string | The memory identifier to update, exactly as returned by a prior recall. |
| `content` | string | The new content that replaces the existing memory. |

**Operating rule shown:** `memoryId` is expected to come from a prior recall and should not be invented or modified.

**Status:** Observed and documented from supplied UI screenshot.

**Runtime questions:** Exact update result/return shape, behavior for nonexistent or unauthorized `memoryId`, atomicity, persistence timing, and whether scope is always preserved have not been established.

### 8. Export Excel V2

**Tool name:** `export_excel_v2`

**Purpose:** Generates a multi-sheet Excel `.xlsx` workbook from structured data. The supplied configuration description establishes the workbook structure, supported cell values, formulas, sheet options, formatting, conditional formatting, and chart definitions.

**Core output rule:** The `filename` parameter is used as the output filename and should contain only letters, numbers, spaces, and hyphens.

**Required per sheet:**

| Field | Type | Observed rule |
|---|---|---|
| `name` | string | Sheet tab label. |
| `data` | array | List of rows, where each row is a list of cell values. |

**Allowed cell values:**

- string: written as text.
- number (integer/float): written as a number.
- boolean: written as TRUE/FALSE.
- ISO date string such as `2026-04-28`: written as a date.
- formula string beginning with `=`: treated as an Excel formula using standard A1 references.
- `null`: skipped, leaving an empty cell.

**Formula examples supplied:**

```text
=SUM(B2:B5)
=AVERAGE(C2:C10)
=B2*C2
=B2-C2
=D2/B2
=IF(B2>1000, "High", "Low")
```

**Workbook behavior shown:** The first sheet in the `sheets` list is active when the workbook opens.

**Optional sheet fields:**

| Field | Observed behavior |
|---|---|
| `headers` | Boolean; styles first row as a bold header. Default is auto-detected when the first row is all strings and later rows contain numbers. |
| `freeze_panes` | Object such as `{ "row": 1, "col": 0 }`; freezes rows above and columns left of the specified cell. |
| `autofilter` | Boolean; adds filter dropdowns to the header row and requires headers. |
| `tab_color` | Hex string such as `#4472C4` for the sheet tab. |
| `column_widths` | `auto` or an explicit list of widths. Auto sizing accounts for rendered formatted-number width. |

**Formatting:**

- `header_format` supports `bold`, `italic`, `bg_color`, `font_color`, `align`, and `font_size`.
- Default header formatting shown: bold, blue background `#4472C4`, white font, centered.
- `cell_formats` is a column-scoped list. Each item targets one or more zero-based columns across all data rows.
- Common cell-format keys include `num_format`, `align`, `bold`, `italic`, `font_color`, and `bg_color`.
- Percentage formats such as `0.0%` and `0.00%` expect stored values as fractions such as `0.85`, not `85`.
- Common number/date formats include `#,##0`, `#,##0.00`, `$#,##0`, `$#,##0.00`, `0.0%`, `0.00%`, and `yyyy-mm-dd`.

**Conditional formatting:**

- `conditional_formats` is a list of highlight rules targeting zero-based columns.
- Supported criteria shown: `>`, `<`, `>=`, `<=`, `==`, `!=`.
- Multiple rules can stack on the same column.
- Example traffic-light styles supplied include green, yellow, and red background/font combinations.

**Charts:**

`charts` is a list of chart definitions embedded in the sheet in which they are defined. Required keys are `type`, `categories_col`, `series_cols`, and `position`.

Supported chart types shown:

- `bar` - horizontal.
- `column` - vertical.
- `line`.
- `pie`.

Optional chart properties include `title`, `data_start_row`, `data_end_row`, `width`, and `height`. Series names are auto-read from the header row when `headers=true`. Default chart size shown is 480 x 288 pixels. `position` anchors the chart at a cell such as `F2` or `A10`.

**Minimal structure supplied:**

```text
sheets=[
  {"name":"Sales","data":[
    ["Product","Revenue"],
    ["Widget A",1000],
    ["Widget B",2500],
    ["Total","=SUM(B2:B3)"]
  ]},
  {"name":"Notes","data":[["Q1 review pending"]]}
]
```

**Status:** Configuration contract supplied by the user and documented here as evidence. This is not yet a runtime-verified contract.

**Runtime questions:** Exact parameter schema including whether any fields are mandatory beyond the documented per-sheet `name` and `data`, file naming sanitization behavior, formula calculation/recalculation behavior, exact date parsing/timezone handling, null/empty-string handling, formatting precedence, conditional-format evaluation timing, chart rendering compatibility, maximum workbook/sheet/row limits, behavior for malformed formulas or invalid formats, and returned output/file metadata remain to be tested.

## Updated evidence interpretation

The supplied evidence establishes detailed configuration-level evidence for the following Agent tools:

- `get_reference_file`
- `get_table_schema`
- `resolve_field_value`
- `search_table_data`
- `recall_memory`
- `save_memory`
- `update_memory`
- `export_excel_v2`

They do **not** by themselves establish full runtime behavior. Runtime claims remain subject to verification records.

## Related

- [Agent Node](../02-Orchestration-Primitives/Agent.md)
- [Testing](../11-Testing/README.md)
- [Tools](../04-Tools/README.md)
- [Data and State](../05-Data-State/README.md)
- [Excel V2 verification](../04-Verification/Excel-V2-Verification.md)
