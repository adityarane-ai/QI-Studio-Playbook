# Export Tool Catalog

This document normalizes the export tools supplied during the investigation. It is a tool-contract reference, not a substitute for runtime testing.

## Export Excel V2

**Tool:** `export_excel_v2`

Generates a multi-sheet `.xlsx` workbook from structured sheet data.

Required top-level inputs:

- `filename`
- `sheets`

Per-sheet required fields:

- `name`
- `data`

Supported cell values include strings, numbers, booleans, ISO dates, Excel formulas beginning with `=`, and `null` for blank cells.

Optional sheet capabilities include headers, freeze panes, autofilter, tab color, column widths, header formatting, column-scoped cell formats, conditional formatting, and embedded charts.

Important behavior from the supplied contract:

- Percentage values must be stored as fractions when using percentage number formats.
- Formula references use standard Excel A1 notation.
- The first sheet in the `sheets` array is active when the workbook opens.
- Export output is appended to `system.files` through the configured variable update.

## Export PowerPoint V2

**Tool:** `export_powerpoint_v2`

Generates a branded `.pptx` from structured slide content.

Required top-level inputs:

- `title`
- `slides`

Templates:

- `default`
- `gep`
- `business_blue_black`

The supplied contract explicitly defines supported layouts per template. Do not use an unsupported layout and rely on fallback behavior.

Export output is appended to `system.files` through the configured variable update.

## Export PDF V2

**Tool:** `export_pdf_v2`

Generates a polished PDF from structured sections.

Required top-level inputs:

- `title`
- `sections`

Supports covers, TOC, headings, paragraphs, lists, tables, images, charts, page breaks, metadata blocks, callouts, and endnotes, plus page configuration, headers, footers, and style overrides.

The supplied contract states that image URLs must point directly to image binaries and that CJK characters are not supported by the default body font.

Export output is appended to `system.files`.

## Export Word V2

**Tool:** `export_word_v2`

Generates a `.docx` from structured sections.

Required top-level inputs:

- `title`
- `sections`

Adds Word-specific capabilities including native editable charts, Word TOC fields, bookmarks, cross-references, footnotes, section breaks, custom styles, and document metadata.

The supplied contract states that the TOC is evaluated by Word on open, with `update_fields_on_open` defaulting to `true`.

Export output is appended to `system.files`.

## Export HTML V2

**Tool:** `export_html_v2`

Generates a self-contained styled HTML page.

Required top-level inputs:

- `title`
- `sections`

Themes:

- `light`
- `dark`
- `corporate`

Supports hero sections, stat cards, tables, charts, callouts, code blocks, timelines, two-column layouts, images, and dividers.

The supplied contract explicitly says to use this proactively when a dashboard, report, summary page, visualization, or visually structured HTML response would be more useful than plain text.

## File extraction

**Tool:** `extract_document_to_markdown`

Parses PDF, Word, PowerPoint, legacy Office files, and image inputs into markdown. The supplied contract describes a PDF pipeline involving MarkItDown, image extraction and ordering, vision descriptions, optional OCR for scanned pages, and final upload of extracted markdown.

Required inputs:

- `fileId`
- `fileName`

Optional inputs:

- `bpc`
- `sessionId`

The returned envelope identifies the generated extracted-text file.

## Evidence boundary

The above reflects the supplied tool definitions. Runtime semantics such as exact returned JSON shapes, file identifiers, export failure modes, and downstream attachment behavior remain testable claims unless separately observed.
