# Evidence Record: Export Excel V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-EXCEL-V2-001  
**Date:** 2026-08-23  
**Capability:** Multi-sheet Excel workbook export from structured data.  
**Source:** User-supplied QI Studio tool configuration JSON and tool description.

## What was observed

The `Export Excel V2` tool generates a multi-sheet Excel `.xlsx` workbook from structured data.

### Tool identity and runtime configuration observed

- Tool name: `export_excel_v2`
- Display title: `Export Excel V2`
- Tool type: `mcp`
- Enabled: `true`
- System/export tool metadata identifies it as an export capability.
- The configured input schema requires `filename` and `sheets`.
- `returnDirect` is configured as `false`.
- The configured variable update appends `{{nodeOutput}}` to `system.files`.

### Top-level input schema

Observed JSON schema:

- `filename`: string, required.
- `sheets`: array, required.

The schema leaves `sheets.items` unconstrained at JSON-Schema level. The detailed sheet and cell contract is supplied in the tool description.

### Filename

The `filename` parameter is used as the output filename. The contract says to use only letters, numbers, spaces, and hyphens.

### Required sheet fields

Each sheet requires:

- `name`: string sheet-tab label.
- `data`: list of rows, where each row is a list of cell values.

The first sheet in the input list is active when the workbook opens.

### Allowed cell values

The contract explicitly documents these cell value types:

- string: written as text.
- number, integer or float: written as a number.
- boolean: written as TRUE/FALSE.
- ISO date string such as `2026-04-28`: written as a date.
- formula string beginning with `=`: treated as an Excel formula using standard A1 references.
- `null`: skipped, producing an empty cell.

### Formula support

Documented examples include:

- `=SUM(B2:B5)`
- `=AVERAGE(C2:C10)`
- `=B2*C2`
- `=B2-C2`
- `=D2/B2`
- `=IF(B2>1000, "High", "Low")`

The contract states that formulas use standard A1 cell references.

### Sheet structure options

Optional, case-sensitive fields include:

- `headers`: boolean. Styles the first row as a bold header. Default is auto-detected: true when the first row is all strings while later rows contain numbers.
- `freeze_panes`: object `{row, col}`. Freezes rows above and columns left of the specified cell.
- `autofilter`: boolean. Adds filter dropdowns to the header row when headers are enabled.
- `tab_color`: hex color string such as `#4472C4`.
- `column_widths`: `auto` by default, or an explicit list of column widths.

### Header formatting

`header_format` applies to the header row. Documented keys are:

- `bold`
- `italic`
- `bg_color`
- `font_color`
- `align`
- `font_size`

The documented default is bold white text on `#4472C4`, centered.

### Cell formatting

`cell_formats` is a list of column-scoped format specifications. Each entry identifies one or more zero-based columns and applies the format across all data rows in those columns.

Documented common keys:

- `num_format`
- `align`
- `bold`
- `italic`
- `font_color`
- `bg_color`

The contract states that column-scoped formatting means different number formats should be placed in separate columns/specifications.

### Documented number formats

Examples include:

- `#,##0`
- `#,##0.00`
- `$#,##0`
- `$#,##0.00`
- `0.0%`
- `0.00%`
- `yyyy-mm-dd`

Percentage values must be stored as fractions such as `0.85`, not `85`, because the percent format multiplies by 100 at display time.

### Conditional formatting

`conditional_formats` is documented as a list of highlight rules targeting zero-based columns. Multiple rules may stack on the same column.

Supported comparison criteria:

- `>`
- `<`
- `>=`
- `<=`
- `==`
- `!=`

The contract supplies standard green, yellow, and red traffic-light palettes and a `format` object containing formatting properties.

### Charts

`charts` embeds chart definitions in the sheet on which they are defined.

Required chart keys:

- `type`
- `categories_col`
- `series_cols`
- `position`

Optional chart keys:

- `title`
- `data_start_row`
- `data_end_row`
- `width`
- `height`

Supported chart types:

- `bar`
- `column`
- `line`
- `pie`

`categories_col` and `series_cols` use zero-based column indices. `position` is an Excel cell anchor such as `F2`. The contract recommends placing charts to the right of the data to avoid overlap.

Default chart data range behavior is documented as starting at row 1 when headers are enabled, otherwise row 0, with the last data row as the default end row. Default chart dimensions are 480 x 288 pixels.

Series names are auto-read from the header row when `headers=true`.

## Evidence status

**Status:** Configuration contract captured from supplied tool description and JSON configuration.

This evidence establishes the documented input schema, sheet structure, cell types, formula syntax, formatting options, conditional formatting, chart configuration, filename rule, output-variable wiring, and stated defaults as configured/documented behavior.

## What this evidence does not prove

The supplied configuration does not establish exact runtime behavior for:

- actual workbook generation and file integrity,
- filename sanitization/error behavior,
- formula calculation and recalculation behavior in Excel-compatible readers,
- date parsing edge cases and timezone behavior,
- auto-detection of headers in mixed-content sheets,
- freeze-pane semantics at unusual row/column indices,
- autofilter behavior with blank headers,
- exact column-width measurement and rendered number widths,
- conditional-format rendering fidelity and rule ordering,
- chart rendering, overlap handling, or unsupported data combinations,
- empty sheets, irregular row lengths, or very large workbooks,
- success output payload and exact file reference/path,
- persistence behavior of the configured `system.files` append,
- exact validation/error payloads and limits.

## Related verification

See `04-Verification/Export-Excel-V2-Verification.md`.
