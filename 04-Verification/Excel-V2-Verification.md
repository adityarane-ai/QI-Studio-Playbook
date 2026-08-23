# Excel V2 Tool Verification

> Living verification record for `export_excel_v2`. The supplied configuration defines the intended contract, but the items below require runtime confirmation before being treated as verified behavior.

## EV2-001: Complete parameter schema

**Current understanding:** The configuration requires a `filename` and `sheets`, and each sheet requires `name` and `data`. Optional sheet fields include headers, freeze panes, autofilter, tab color, column widths, formatting, conditional formatting, and charts.

**Needs verification:** Exact tool parameter schema, required/optional flags, default values, and whether any additional top-level parameters exist.

**Test:** Inspect the runtime tool schema and run minimal valid and intentionally incomplete payloads.

## EV2-002: Filename validation and sanitization

**Current understanding:** The configured rule says the filename should use only letters, numbers, spaces, and hyphens.

**Needs verification:** Whether invalid characters are rejected, removed, normalized, or silently replaced, and whether the extension is automatically added.

**Test:** Use valid names, names with underscores, punctuation, Unicode, path separators, duplicate extensions, and empty names.

## EV2-003: Workbook/sheet limits

**Current understanding:** The tool creates multi-sheet `.xlsx` workbooks.

**Needs verification:** Maximum number of sheets, rows, columns, total cells, and practical workbook size.

**Test:** Controlled load tests with progressively larger workbooks.

## EV2-004: First-sheet activation

**Current understanding:** The first sheet in `sheets` is active when the workbook opens.

**Needs verification:** Runtime workbook `activeTab` behavior and what happens with zero sheets or invalid sheet ordering.

**Test:** Generate multi-sheet workbooks with reordered sheets and inspect the active sheet.

## EV2-005: Cell type coercion

**Current understanding:** Strings, numbers, booleans, ISO dates, formulas, and null are supported.

**Needs verification:** Exact handling of numeric edge cases, booleans, empty strings, whitespace, non-ISO date strings, and mixed-type rows.

**Test:** Generate controlled cells covering each supported type plus invalid/ambiguous values.

## EV2-006: ISO date parsing

**Current understanding:** ISO date strings such as `2026-04-28` are written as dates, and `yyyy-mm-dd` can be used as a number format.

**Needs verification:** Whether dates are stored as real Excel serial dates, exact parsing rules, timezone handling, and support for datetime variants.

**Test:** Inspect the resulting workbook cell types and values for date-only and datetime strings.

## EV2-007: Formula execution and recalculation

**Current understanding:** Strings beginning with `=` are treated as Excel formulas using standard A1 references.

**Needs verification:** Whether formulas are only written or also recalculated before delivery, which formula families are accepted, and how formula errors are handled.

**Test:** Use SUM, AVERAGE, arithmetic, IF, invalid references, and formulas referencing other sheets; inspect formulas and cached values.

## EV2-008: Formula row/column reference behavior

**Current understanding:** Standard Excel A1 references are supported.

**Needs verification:** Absolute/relative references, cross-sheet references, ranges, quoted sheet names, and formulas with commas/semicolons in function arguments.

**Test:** Run formulas with `$A$1`, `A1`, `Sheet2!B2`, and cross-sheet ranges.

## EV2-009: Header auto-detection

**Current understanding:** `headers` defaults to auto-detection when the first row is all strings while later rows contain numbers.

**Needs verification:** Exact detection algorithm, behavior for mixed first rows, all-string data, empty data, and header-only sheets.

**Test:** Generate representative first-row patterns and inspect resulting header styling/filter behavior.

## EV2-010: Freeze panes semantics

**Current understanding:** `freeze_panes: {row:1,col:0}` freezes the header row; the values represent rows above and columns left of the given cell.

**Needs verification:** Exact zero-based interpretation, behavior for larger row/col values, and invalid values.

**Test:** Generate workbooks with several freeze-panes combinations and inspect the workbook view settings.

## EV2-011: Autofilter behavior

**Current understanding:** `autofilter: true` adds filter dropdowns to the header row and requires headers.

**Needs verification:** Whether autofilter is rejected or ignored when headers are false, exact filter range, and behavior with empty/partial rows.

**Test:** Compare valid header sheets with header-disabled and empty-sheet cases.

## EV2-012: Column width auto-sizing

**Current understanding:** `column_widths: "auto"` sizes columns to fit content, accounting for rendered formatted-number width.

**Needs verification:** Maximum width, minimum width, treatment of formulas, dates, long text, empty cells, and formatted values.

**Test:** Compare widths for long text, numbers with formats, dates, formulas, and null cells.

## EV2-013: Explicit column width mapping

**Current understanding:** Explicit widths can be supplied as a list such as `[15,20,10]`, using zero-based columns.

**Needs verification:** Behavior when fewer or more widths than columns are provided and handling of invalid/negative widths.

**Test:** Deliberately mismatch width-list length and inspect workbook behavior.

## EV2-014: Header formatting precedence

**Current understanding:** `header_format` styles the header row and supports bold, italic, background color, font color, alignment, and font size.

**Needs verification:** Precedence between header_format and cell_formats when they both target the header, and handling of partial/invalid format objects.

**Test:** Apply conflicting header and column formats and inspect final cell styles.

## EV2-015: Cell-format scope and row range

**Current understanding:** `cell_formats` is column-scoped across all data rows and uses zero-based column indices.

**Needs verification:** Whether the header is excluded exactly as described, how sparse rows are treated, and how conflicting cell-format entries are resolved.

**Test:** Apply multiple formats to the same column and compare header/data rows.

## EV2-016: Number-format rendering

**Current understanding:** Standard formats such as `#,##0`, `#,##0.00`, USD, percentages, and ISO dates are supported.

**Needs verification:** Exact Excel number-format strings accepted, locale behavior, and rendering of negative/zero values.

**Test:** Generate representative values using each documented format and inspect the resulting workbook.

## EV2-017: Percentage storage/display rule

**Current understanding:** Percentage values must be stored as fractions such as `0.85` and formatted using `0.0%` to render `85.0%`.

**Needs verification:** Whether the exporter enforces, transforms, or simply writes the supplied numeric value and format.

**Test:** Compare 0.85 and 85 under the same percentage format.

## EV2-018: Conditional-format evaluation

**Current understanding:** Conditional rules support `>`, `<`, `>=`, `<=`, `==`, and `!=`, and multiple rules can stack on a column.

**Needs verification:** Evaluation against formulas, blanks, strings, dates, booleans, and multiple overlapping rules; precedence when rules conflict.

**Test:** Exercise each criteria operator and overlapping rules on representative data.

## EV2-019: Conditional-format range calculation

**Current understanding:** Row ranges are auto-computed from the data and rules target columns by zero-based index.

**Needs verification:** Exact first/last row covered, interaction with headers, empty rows, and aggregate/formula rows.

**Test:** Inspect generated conditional-format ranges for headered and non-headered sheets.

## EV2-020: Chart series and category mapping

**Current understanding:** Charts require `type`, `categories_col`, `series_cols`, and `position`; series names are read from headers when headers are enabled.

**Needs verification:** Exact mapping of zero-based data columns to Excel chart ranges, handling of formulas, blanks, and headers.

**Test:** Generate each chart type with one and multiple series and inspect chart XML/ranges.

## EV2-021: Chart placement and sizing

**Current understanding:** `position` anchors the chart top-left and defaults are 480 x 288 pixels.

**Needs verification:** Exact pixel-to-Excel conversion, overlap behavior, invalid positions, and behavior when width/height are omitted.

**Test:** Create charts at several positions and dimensions and inspect workbook layout.

## EV2-022: Chart data-row defaults

**Current understanding:** `data_start_row` defaults to 1 when headers exist and 0 otherwise; `data_end_row` defaults to the last data row.

**Needs verification:** Exact zero-based interpretation and whether explicitly supplied ranges override header detection consistently.

**Test:** Compare headered and non-headered sheets with explicit and default row bounds.

## EV2-023: Malformed chart configuration

**Current understanding:** Charts have required keys and supported types.

**Needs verification:** Whether malformed chart definitions fail the whole export, skip the chart, or partially generate the workbook.

**Test:** Omit required fields, use invalid chart types, invalid columns, and invalid positions.

## EV2-024: Null and empty-cell semantics

**Current understanding:** `null` is skipped and leaves an empty cell.

**Needs verification:** Difference between null, empty string, whitespace-only string, and missing array elements, including effects on formulas, filters, widths, and charts.

**Test:** Place all variants side by side and inspect resulting workbook cells and dependent features.

## EV2-025: Error handling and partial outputs

**Current understanding:** The configuration describes valid input structures but does not define failure semantics.

**Needs verification:** Whether a single invalid sheet/cell/format fails the whole workbook, produces a partial file, or returns structured errors.

**Test:** Introduce controlled invalid values at sheet, row, formula, formatting, conditional-format, and chart levels.

## EV2-026: Output/file metadata contract

**Current understanding:** The tool generates an `.xlsx` workbook and uses the supplied filename as the output filename.

**Needs verification:** Exact tool return object, file identifier/path, MIME type, size metadata, and downstream file-variable representation.

**Test:** Capture the complete tool response and consume it from a downstream node.

## EV2-027: Workbook compatibility

**Current understanding:** The intended output is an Excel `.xlsx` workbook.

**Needs verification:** Compatibility across Excel desktop, Excel web, and common parsers such as LibreOffice/openpyxl for formulas, formatting, conditional rules, and charts.

**Test:** Open a representative generated workbook in multiple consumers and compare functionality.

## Promotion rule

Do not move these items to **Verified** merely because the generated workbook opens. Promote each item only when the specific runtime behavior has been tested and recorded in the evidence record. Once verified, remove the item from this file and incorporate the confirmed behavior into the canonical Agent/Excel evidence documentation.
