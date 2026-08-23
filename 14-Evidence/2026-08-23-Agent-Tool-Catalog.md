# Agent Tool Catalog and Configuration Evidence

**Evidence date:** 2026-08-23  
**Source:** User-supplied QI Studio Agent tool configuration JSON and screenshots  
**Status:** OBSERVED / DOCUMENTED unless explicitly marked Runtime Confirmed

This document records the Agent tools supplied during the QI Studio reverse-engineering session. It is a configuration/evidence record, not a claim that every runtime behavior has already been tested.

## 1. Export tools

### Export Excel V2

- Name: `export_excel_v2`
- Title: `Export Excel V2`
- Type: tool / MCP
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-export_excel_v2-11`
- Server: `module mcp`
- Export: yes
- Variable update: append `{{nodeOutput}}` to `system.files`
- Required top-level inputs: `filename`, `sheets`

#### Supported sheet structure

Required per sheet:
- `name`
- `data`

Optional:
- `headers`
- `freeze_panes`
- `autofilter`
- `tab_color`
- `column_widths`
- `header_format`
- `cell_formats`
- `conditional_formats`
- `charts`

Cell values supported:
- string
- number
- boolean
- ISO date string
- formula string beginning with `=`
- null

Formula examples explicitly documented:
- `=SUM(B2:B5)`
- `=AVERAGE(C2:C10)`
- `=B2*C2`
- `=B2-C2`
- `=D2/B2`
- `=IF(B2>1000, "High", "Low")`

Formatting supports column-scoped number formats, alignment, bold/italic, font color, and background color. Percentage values are stored as fractions, then formatted with `%`.

Conditional formatting supports `>`, `<`, `>=`, `<=`, `==`, `!=` and standard green/yellow/red formats.

Charts support `bar`, `column`, `line`, and `pie`, with category and series columns and an Excel cell position.

**Runtime status:** configuration documented; targeted end-to-end export execution still needs verification unless a separate verification record says otherwise.

### Export PowerPoint V2

- Name: `export_powerpoint_v2`
- Title: `Export PowerPoint V2`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-export_powerpoint_v2-10`
- Required inputs: `title`, `slides`
- Optional: `template`
- Variable update: append `{{nodeOutput}}` to `system.files`

Supported templates:
- `default`
- `gep`
- `business_blue_black`

Supported layouts for `default` / `gep`:
- `title_slide`
- `section_header`
- `title_subtitle_and_content`
- `title_and_content`
- `two_content`
- `three_content`
- `title_and_picture`
- `title_and_chart`
- `title_content_and_chart`
- `title_content_and_picture`

Unsupported for `default` / `gep`: `two_content_with_headers`

Supported layouts for `business_blue_black`:
- `title_slide`
- `section_header`
- `title_and_content`
- `two_content`
- `two_content_with_headers`
- `title_and_picture`
- `title_and_chart`
- `title_content_and_chart`
- `title_content_and_picture`

Unsupported for `business_blue_black`:
- `title_subtitle_and_content`
- `three_content`

Charts support `bar`, `bar_stacked`, `column`, `line`, `pie`.

### Export PDF V2

- Name: `export_pdf_v2`
- Title: `Export PDF V2`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-export_pdf_v2-12`
- Required: `title`, `sections`
- Variable update: append `{{nodeOutput}}` to `system.files`

Supported section types:
- `cover`
- `toc`
- `heading`
- `paragraph`
- `bullet_list`
- `numbered_list`
- `table`
- `image`
- `chart`
- `spacer`
- `horizontal_rule`
- `page_break`
- `key_value`
- `callout`
- `endnote_ref`
- `endnotes`

Page configuration supports `letter`, `a4`, `legal`, `tabloid`, portrait/landscape, and point-based margins.

The tool supports cover pages, headers/footers with page macros, auto-populated TOC from headings, endnotes, inline text markup, style overrides, images, and charts.

Limits stated in the supplied configuration:
- 500 sections
- 2000 table rows
- 10 MB per image
- 15 second image fetch timeout

### Export Word V2

- Name: `export_word_v2`
- Title: `Export Word V2`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-export_word_v2-13`
- Required: `title`, `sections`
- Variable update: append `{{nodeOutput}}` to `system.files`

Supported section types:
- `cover`
- `toc`
- `heading`
- `paragraph`
- `bullet_list`
- `numbered_list`
- `table`
- `image`
- `chart`
- `hyperlink`
- `page_break`
- `section_break`
- `horizontal_rule`
- `key_value`
- `callout`
- `footnote_ref`
- `bookmark`
- `cross_reference`

Additional document controls include subject, keywords, custom styles, style overrides, native Word charts, TOC field injection, bookmarks, cross-references, footnotes, section breaks, and `update_fields_on_open`.

Limits stated in the supplied configuration:
- 500 sections
- 2000 table rows
- 10 MB per image
- 15 second image fetch timeout

### Export HTML V2

- Name: `export_html_v2`
- Title: `Export HTML V2`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-export_html_v2-14`
- Required: `title`, `sections`
- Optional: `theme`, `accent_color`
- Variable update: append `{{nodeOutput}}` to `system.files`

The supplied tool guidance explicitly says to call this proactively for dashboards, reports, summaries, visual pages, structured data presentation, and visually rich outputs where HTML is more useful than plain text.

Section types:
- `hero`
- `section`
- `text`
- `bullet_list`
- `stat_cards`
- `table`
- `chart`
- `callout`
- `code_block`
- `timeline`
- `two_column`
- `image`
- `divider`

Themes:
- `light`
- `dark`
- `corporate`

Chart support uses Chart.js and includes `bar`, `column`, `line`, `pie`, and `doughnut`.

## 2. Document/file handling tools

### Extract Document to Markdown

- Name: `extract_document_to_markdown`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-extract_document_to_markdown-15`
- Required: `fileId`, `fileName`
- Optional: `bpc`, `sessionId`
- Type: external MCP

Supported input formats:
- PDF
- Word `.docx`
- PowerPoint `.pptx`
- Legacy `.doc`
- Legacy `.ppt`
- PNG/JPG/JPEG/GIF/WEBP/BMP/TIF/TIFF

PDF pipeline stated in the supplied description:
1. MarkItDown extraction
2. PyMuPDF image extraction and bounding boxes
3. Header/footer/watermark filtering
4. Composite stitching for overlaps
5. OCR for image-dominant scanned pages
6. Vision LLM image descriptions
7. Reading-order merge
8. Markdown upload to BlobProxy

Returned envelope:
- `fileId`
- `fileName`
- `filePath`

### Conversation Attachment

- Name: `ConversationAttachment`
- Title: `Conversation Attachment`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-ConversationAttachment-20`
- Required: `fileId`
- Returns the uploaded session file content as a string.
- The supplied description says to call repeatedly for multiple attachments.

The same configuration was supplied twice in conversation; it is one tool definition, not two separate tools.

### Export File

- Name: `ExportBlob`
- Title: `Export File`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-ExportBlob-21`
- Required: `fileId`
- Returns `Name` and `Id` for an existing blob file.
- Variable update: append `{{nodeOutput}}` to `system.files`
- Supplied guidance states that the returned object must be included in the final response `attachments` array.

## 3. Web and memory tools

### Web Search

- Name: `BraveWebSearch`
- Title: `Web Search`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-BraveWebSearch-16`
- Required: `query`, `searchFromDate`
- `searchFromDate` accepts string/null date-time.
- Returns search results with `Name`, `Link`, and `Value`.
- Important supplied rule: if results are used in a final answer, the source links must also be placed in `citations` using the link as `citation.path`.

### Set In Memory

- Name: `Store`
- Title: `Set In Memory`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-Store-17`
- Required: `key`, `value`
- Stores a key-value pair in the memory bag for the current context.
- Supplied safety rule: only use when there are clear and explicit instructions to store data in memory.

### Get From Memory

- Name: `Retrieve`
- Title: `Get From Memory`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-Retrieve-18`
- Required: `key`
- Retrieves the value associated with the key.
- Supplied safety rule: only use when there are clear and explicit instructions to retrieve memory data.

## 4. Email

### Send Email

- Name: `SendEmail`
- Title: `Send Email`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-SendEmail-19`
- Required: `tos`, `subject`, `emailBody`
- Optional: `ccs`, `bccs`, `replyTo`, `sender`, `attachments`

Recipient objects require `email`; optional recipient metadata includes first name, last name, contact code, registration status, user type, and legal company name.

`emailBody` must be complete valid HTML with inline styles only. The supplied configuration explicitly requires closing the email with `Regards, GEP Quantum` and forbids scripts/external resources.

Attachments are objects with `name` and `id` identifying blob-storage files, including export-tool outputs.

**Runtime status:** exact send behavior, recipient validation, attachment handling, and downstream result semantics remain candidates for controlled tests.

## 5. System-tool discovery and execution layer

### Search System Tools

- Name: `SearchSystemTools`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-SearchSystemTools-26`
- Required: `intent`
- Purpose: discover matching system tools.
- Supplied rule: call FIRST before a system action and do not guess tool names.

### Get System Tool Schema

- Name: `GetSystemToolSchema`
- Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-GetSystemToolSchema-27`
- Required: `toolNames` array
- Purpose: retrieve complete parameter schemas after discovery and before execution.

### Execute System Tool

- Name: `ExecuteSystemTool`
- Tool ID: `894de768-1ac3-48a9-8210-89f6c2e33ae6-ExecuteSystemTool-28` 
- Required: `tool_name`, `arguments`
- Purpose: execute a system tool using the discovered name and matching schema.

Canonical execution pattern supplied by the tool descriptions:

```text
SearchSystemTools
      ↓
GetSystemToolSchema
      ↓
ExecuteSystemTool
```

Do not bypass the discovery/schema steps when the Agent is operating through this system-tool layer.

## 6. Knowledge workflow prerequisite

### get-knowledge-workflow-instructions

- Name: `get-knowledge-workflow-instructions`
- Tool ID: `get-knowledge-workflow-instructions`
- Type: MCP prompt
- Purpose: returns essential instructions for querying knowledge sources.
- Required inputs: none.
- Supplied mandatory rule: call this FIRST before any knowledge-related tools such as library metadata or table schema tools, and at the start of every Agent invocation involving knowledge sources.

## 7. Tool-governance model established

The combined tool set implies a repeatable governance chain:

```text
Need identified
   ↓
Discover capability / tool
   ↓
Inspect exact schema
   ↓
Execute with explicit arguments
   ↓
Capture result
   ↓
Map result into variables/state
   ↓
Validate downstream consumption
   ↓
Expose file / citation / user response as required
```

This is a design principle, not proof that every individual tool enforces each step automatically.

## 8. Evidence boundary

The supplied JSON establishes configuration contracts, parameter schemas, descriptions, output expectations, and declared variable updates. It does **not** automatically prove successful runtime execution for every capability.

Runtime confirmation should therefore be recorded separately in `04-Verification/` and should cover:

1. tool invocation succeeds
2. output object/value is actually produced
3. output is stored where expected
4. downstream node can consume it
5. final user-visible/file-visible result is correct where applicable
