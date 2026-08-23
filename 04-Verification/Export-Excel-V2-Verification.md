# Verification Queue: Export Excel V2

**Capability:** `export_excel_v2`  
**Evidence date:** 2026-08-23  
**Source:** Supplied QI Studio tool configuration JSON and tool description.  
**Status:** Configuration captured; runtime behavior pending verification.

## Input and output

- [ ] Verify minimal workbook generation with one sheet and one cell.
- [ ] Verify `filename` is used as the generated output filename.
- [ ] Verify allowed filename characters: letters, numbers, spaces, hyphens.
- [ ] Verify disallowed filename characters such as `/`, `:`, `?`, `*`, `#` are rejected or sanitized deterministically.
- [ ] Verify missing `filename` is rejected clearly.
- [ ] Verify missing `sheets` is rejected clearly.
- [ ] Verify an empty `sheets` array behavior.
- [ ] Capture exact success output shape and file reference/path.
- [ ] Verify configured `system.files` append receives the actual export node output.

## Sheet structure

- [ ] Verify every sheet requires `name` and `data`.
- [ ] Verify the first sheet is active on workbook open.
- [ ] Verify multiple sheets preserve input order.
- [ ] Verify duplicate sheet names behavior.
- [ ] Verify invalid or overlong sheet names behavior.
- [ ] Verify empty sheets and empty data arrays.
- [ ] Verify irregular row lengths across the same sheet.

## Cell values

- [ ] Verify string values are written as text.
- [ ] Verify integer values are written as numeric cells.
- [ ] Verify float values are written as numeric cells with expected precision.
- [ ] Verify booleans are written as TRUE/FALSE boolean cells.
- [ ] Verify ISO date strings are written as Excel dates.
- [ ] Verify `null` produces an empty/skipped cell.
- [ ] Verify mixed value types within one row.
- [ ] Verify empty strings versus nulls are handled distinctly.
- [ ] Verify unsupported nested objects/arrays are rejected or handled predictably.

## Formulas

- [ ] Verify formula strings beginning with `=` are stored as Excel formulas.
- [ ] Verify SUM formula calculation in a generated workbook.
- [ ] Verify AVERAGE formula calculation.
- [ ] Verify arithmetic formulas such as multiplication, subtraction, and division.
- [ ] Verify IF formulas with quoted text branches.
- [ ] Verify formulas use standard A1 references.
- [ ] Verify formula cells have expected number/formula metadata when inspected.
- [ ] Verify whether the exporter calculates formulas or relies on Excel recalculation.
- [ ] Verify malformed formulas are rejected or preserved predictably.

## Header and sheet structure options

- [ ] Verify `headers=true` styles the first row as a header.
- [ ] Verify `headers=false` suppresses header styling.
- [ ] Verify automatic header detection behavior exactly as documented.
- [ ] Verify mixed first-row content affects auto-detection as expected.
- [ ] Verify `freeze_panes` freezes specified rows and columns.
- [ ] Verify `autofilter=true` adds filters when headers are present.
- [ ] Verify `autofilter=true` with headers disabled.
- [ ] Verify `tab_color` applies to the worksheet tab.
- [ ] Verify `column_widths="auto"` sizing.
- [ ] Verify explicit `column_widths` sizing and handling of mismatched list length.

## Header and cell formatting

- [ ] Verify all documented `header_format` keys.
- [ ] Verify documented default header formatting.
- [ ] Verify `cell_formats` applies by zero-based column index.
- [ ] Verify one `cell_formats` entry can target multiple columns.
- [ ] Verify formatting applies across all data rows only as documented.
- [ ] Verify `num_format` rendering for integers, decimals, USD, percentages, and dates.
- [ ] Verify percentage values must be stored as fractions for correct display.
- [ ] Verify alignment, bold, italic, font color, and background color.
- [ ] Verify invalid format values are rejected or ignored predictably.

## Conditional formatting

- [ ] Verify each comparison criterion: `>`, `<`, `>=`, `<=`, `==`, `!=`.
- [ ] Verify rule targeting by zero-based column.
- [ ] Verify multiple rules stack on the same column.
- [ ] Verify supplied green/yellow/red formats render correctly.
- [ ] Verify conditional formatting on formula-result cells.
- [ ] Verify behavior with blanks, text, and mixed types in targeted columns.

## Charts

- [ ] Verify `bar`, `column`, `line`, and `pie` charts.
- [ ] Verify required chart keys `type`, `categories_col`, `series_cols`, `position`.
- [ ] Verify optional `title`.
- [ ] Verify `data_start_row` and `data_end_row` overrides.
- [ ] Verify default chart row range with and without headers.
- [ ] Verify custom width and height.
- [ ] Verify chart series are read from the requested zero-based columns.
- [ ] Verify series names auto-read from headers when `headers=true`.
- [ ] Verify multiple chart series.
- [ ] Verify pie chart behavior.
- [ ] Verify empty, negative, decimal, and mismatched chart data.
- [ ] Verify charts at different anchor positions and overlapping/adjacent data.

## Workbook integrity

- [ ] Verify generated file is a valid `.xlsx` package.
- [ ] Verify workbook opens in Excel or a compatible reader without repair prompts.
- [ ] Verify formulas remain formulas after save.
- [ ] Verify dates remain dates after save.
- [ ] Verify formatting survives reopening.
- [ ] Verify charts survive reopening.
- [ ] Verify worksheet names, order, active sheet, filters, and freeze panes survive reopening.
- [ ] Verify deterministic output for identical inputs where practical.
- [ ] Verify repeated exports do not corrupt or overwrite unrelated files.

## Scale and limits

- [ ] Identify practical worksheet row/column limits exposed by the tool.
- [ ] Test large row counts and large cell payloads.
- [ ] Test many sheets in one workbook.
- [ ] Test very large formulas and conditional-format sets.
- [ ] Test many charts in one sheet/workbook.
- [ ] Capture timeouts or size limits if observed.

## Error handling

- [ ] Test malformed sheet objects.
- [ ] Test missing required sheet fields.
- [ ] Test invalid chart types.
- [ ] Test invalid formatting structures.
- [ ] Test invalid column indices.
- [ ] Test invalid freeze-pane objects.
- [ ] Test invalid `tab_color` values.
- [ ] Capture exact rejection/error messages and output behavior.

## Evidence promotion rule

A verification item moves from this queue into the evidence/confirmed area only after a concrete runtime observation, reproducible test result, or authoritative product documentation establishes the behavior. Once confirmed, remove the corresponding item from this queue and record the evidence and test context in the relevant evidence file.
