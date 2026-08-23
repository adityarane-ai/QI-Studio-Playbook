# Agent Tool Catalog

**Evidence date:** 2026-08-23  
**Status:** Configuration contracts captured from user-supplied QI Studio tool definitions. Runtime execution is not implied unless separately marked.

## Export tools

| Tool | Required inputs | Key documented capabilities | Artifact wiring |
|---|---|---|---|
| `export_excel_v2` | `filename`, `sheets` | Multi-sheet workbook; typed cells; formulas; headers; panes; filters; formatting; conditional formatting; bar/column/line/pie charts | `system.files` append of `nodeOutput` |
| `export_powerpoint_v2` | `title`, `slides` | Branded PPTX; template-specific layout matrix; charts | `system.files` append |
| `export_pdf_v2` | `title`, `sections` | Structured PDF; TOC; covers; tables; images; charts; headers/footers; endnotes; styles; page settings | `system.files` append |
| `export_word_v2` | `title`, `sections` | DOCX; TOC; styles; tables; images; native charts; hyperlinks; section breaks; bookmarks; cross-references; footnotes | `system.files` append |
| `export_html_v2` | `title`, `sections` | Rich self-contained HTML; hero, tables, cards, timelines, two-column, charts; light/dark/corporate themes | `system.files` append |

### Excel V2

`export_excel_v2` uses `filename` and `sheets`. Each sheet requires `name` and `data`. Cell values documented: string, number, boolean, ISO date string, formula beginning `=`, and null. Formulas use A1 references. Optional sheet controls include headers, freeze panes, autofilter, tab color, column widths, header formatting, column-scoped cell formats, conditional formats and charts. Percentage values are stored as fractions and formatted as percentages.

Charts: bar, column, line, pie. Chart configuration uses zero-based category/series column indices and an Excel cell position.

### PowerPoint V2

`export_powerpoint_v2` requires `title` and `slides`; `template` is optional. Templates observed: `default`, `gep`, `business_blue_black`.

For `default`/`gep`, supported layouts include `title_slide`, `section_header`, `title_subtitle_and_content`, `title_and_content`, `two_content`, `three_content`, `title_and_picture`, `title_and_chart`, `title_content_and_chart`, `title_content_and_picture`. `two_content_with_headers` is unsupported.

For `business_blue_black`, `two_content_with_headers` is supported, while `title_subtitle_and_content` and `three_content` are unsupported.

Chart types: bar, bar_stacked, column, line, pie.

### PDF V2

`export_pdf_v2` supports section types `cover`, `toc`, `heading`, `paragraph`, `bullet_list`, `numbered_list`, `table`, `image`, `chart`, `spacer`, `horizontal_rule`, `page_break`, `key_value`, `callout`, `endnote_ref`, `endnotes`. Page size: letter/a4/legal/tabloid; portrait/landscape. Headers/footers support `{page}`, `{total}`, `{title}`. Stated limits: 500 sections, 2000 table rows, 10 MB/image, 15-second remote image timeout.

### Word V2

`export_word_v2` supports cover, TOC, headings 1-6, paragraphs, nested bullets, numbered lists, rich tables with merges, images, native charts, hyperlinks, page/section breaks, key-value blocks, callouts, footnotes, bookmarks and cross-references. `update_fields_on_open` defaults to true. Stated limits: 500 sections, 2000 table rows, 10 MB/image, 15-second image fetch timeout.

### HTML V2

`export_html_v2` supports hero, section, text, bullet list, stat cards, table, chart, callout, code block, timeline, two-column, image and divider. Themes: light, dark, corporate. Charts: bar, column, line, pie, doughnut. The tool guidance explicitly recommends proactive use when rich visual output is useful. Stated limit: 200 sections.

## Document and file tools

### `extract_document_to_markdown`

Required: `fileId`, `fileName`. Optional: `bpc`, `sessionId`. Supported formats include PDF, DOCX, PPTX, legacy DOC/PPT, and common image formats. The declared PDF pipeline uses MarkItDown, PyMuPDF image extraction, watermark/header-footer filtering, composite stitching, OCR for scanned pages, vision-LM image descriptions, reading-order merging and BlobProxy upload. Declared output envelope: `fileId`, `fileName`, `filePath`.

### `ConversationAttachment`

Required: `fileId`. Returns uploaded session-file content as a string. The supplied definition should be called once per file when multiple files are present. The same JSON was supplied twice in the conversation; it is one tool definition.

### `ExportBlob`

Required: `fileId`. Returns `Name` and `Id` for an existing blob file. The supplied guidance says the returned object should be used in the final response `attachments` array. It appends `nodeOutput` to `system.files`.

## Web, memory and email tools

### `BraveWebSearch`

Required: `query`, `searchFromDate`. Returns `Name`, `Link`, `Value`. If search results are used in a final answer, the source link must also be included in the final citation structure using the link as the citation path.

### `Store` / `Retrieve`

`Store` requires `key`, `value` and is described as setting a value in the memory bag. `Retrieve` requires `key` and returns the stored value. The supplied descriptions explicitly restrict these tools to cases where storage/retrieval is clearly and explicitly requested.

### `SendEmail`

Required: `tos`, `subject`, `emailBody`. Optional: `ccs`, `bccs`, `replyTo`, `sender`, `attachments`. `emailBody` must be valid HTML with inline styles only; no scripts or external resources; close with `Regards, GEP Quantum`. Attachment items contain `name` and `id`.

## System-action discovery layer

```mermaid
flowchart LR
    I[Need a system action] --> S[SearchSystemTools]
    S --> G[GetSystemToolSchema]
    G --> E[ExecuteSystemTool]
    E --> V[Validate output and downstream use]
```

- `SearchSystemTools` requires `intent`; supplied rule says call it first and do not guess system tool names.
- `GetSystemToolSchema` requires `toolNames`; call after discovery and before execution.
- `ExecuteSystemTool` requires `tool_name` and `arguments` matching the discovered schema.

Exact configured ExecuteSystemTool ID from the supplied JSON is `894de768-1ac3-48a9-8210-89f6c2e33ae6-ExecuteSystemTool-28`.

## Knowledge workflow prerequisite

`get-knowledge-workflow-instructions` is an MCP prompt with no inputs. The supplied rule says it must be called first before knowledge-related tools and at the start of every Agent invocation involving knowledge sources.

## Evidence boundary

These records establish the supplied configuration contracts. They do not establish successful runtime execution, exact output payloads, artifact persistence, rendering fidelity, error semantics, or security behavior unless a separate runtime test confirms them.
