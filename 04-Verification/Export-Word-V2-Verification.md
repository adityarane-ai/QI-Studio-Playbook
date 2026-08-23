# Verification Queue: Export Word V2

**Capability:** `export_word_v2`  
**Evidence date:** 2026-08-23  
**Status:** Configuration captured; runtime verification pending.

## Input and filename

- [ ] Verify minimal valid `.docx` generation.
- [ ] Verify `title` is used as the output filename.
- [ ] Verify allowed filename characters: letters, numbers, spaces, hyphens.
- [ ] Verify disallowed filename characters are rejected or handled deterministically.
- [ ] Verify missing/empty `title` behavior.
- [ ] Verify missing `sections` behavior.
- [ ] Verify 499, 500, and 501 section boundaries.

## Section types and schemas

- [ ] Verify every documented section type renders: cover, toc, heading, paragraph, bullet_list, numbered_list, table, image, chart, hyperlink, page_break, section_break, horizontal_rule, key_value, callout, footnote_ref, bookmark, cross_reference.
- [ ] Verify missing `type` or `content` yields a clear error.
- [ ] Verify unknown section types fail cleanly.
- [ ] Verify extra unknown fields are ignored or rejected consistently.
- [ ] Verify input order equals output section order.

## TOC, headings, bookmarks, cross-references, footnotes

- [ ] Verify heading levels 1 through 6 map to Word Heading styles.
- [ ] Verify `toc` levels 1 through 6.
- [ ] Verify `toc.hyperlink` behavior.
- [ ] Verify TOC field is populated on open when `update_fields_on_open=true`.
- [ ] Verify placeholder/update behavior when Word does not refresh fields automatically.
- [ ] Verify `update_fields_on_open=false` behavior.
- [ ] Verify `heading.toc=false` behavior.
- [ ] Verify heading bookmark creation.
- [ ] Verify standalone bookmark section.
- [ ] Verify cross-reference REF fields point to the intended bookmark.
- [ ] Verify missing/invalid bookmark names.
- [ ] Verify native footnote numbering and page-bottom placement.
- [ ] Verify multiple footnotes and long footnotes across pages.

## Text and inline formatting

- [ ] Verify paragraph alignment, font family, size, color, bold, italic, underline, strikethrough, highlight, spacing, and line spacing.
- [ ] Verify all documented inline tags render: bold, italic, underline, strikethrough, color, line break.
- [ ] Verify markdown-style links inside text.
- [ ] Verify malformed/unclosed inline tags.
- [ ] Verify long paragraphs and page wrapping.
- [ ] Verify Unicode and non-English text rendering.

## Lists

- [ ] Verify bullet styles bullet/dash/circle.
- [ ] Verify structured list items with levels and children.
- [ ] Verify numbering styles decimal/roman/letter.
- [ ] Verify custom `start` values.
- [ ] Verify nested list rendering does not lose indentation or hierarchy.
- [ ] Verify list continuation across page breaks.

## Tables

- [ ] Verify normal headers/rows.
- [ ] Verify tables without headers.
- [ ] Verify header styling and alternating rows.
- [ ] Verify borders true/false.
- [ ] Verify width interpretation below 100 as inches and >=100 as points.
- [ ] Verify explicit column widths.
- [ ] Verify cell bold/italic/alignment/background/font-color.
- [ ] Verify rowspan/colspan merging.
- [ ] Verify merged cells across page boundaries.
- [ ] Verify header row repeats on continuation pages.
- [ ] Verify 1999/2000/2001 row boundaries.
- [ ] Verify long cell wrapping.
- [ ] Verify empty tables.
- [ ] Verify invalid merge coordinates.

## Images

- [ ] Verify local image paths.
- [ ] Verify HTTP and HTTPS image URLs.
- [ ] Verify data URIs.
- [ ] Verify invalid URLs/paths.
- [ ] Verify 15-second timeout behavior.
- [ ] Verify 10 MB image-size behavior.
- [ ] Verify width/height interpretation below 100 as inches and >=100 as points.
- [ ] Verify default approximately 6-inch width.
- [ ] Verify image captions, alignment, and wrapping.

## Native Word charts

- [ ] Verify bar, column, line, and pie charts.
- [ ] Verify native chart remains editable in Word.
- [ ] Verify title, labels, multiple series, and values.
- [ ] Verify pie uses first series only.
- [ ] Verify negative, zero, and decimal values.
- [ ] Verify mismatched label/series lengths.
- [ ] Verify non-numeric values.
- [ ] Verify chart sizing and placement.

## Page layout and section breaks

- [ ] Verify letter, A4, legal, tabloid.
- [ ] Verify portrait and landscape.
- [ ] Verify margins in points.
- [ ] Verify top-level page configuration.
- [ ] Verify next_page, continuous, even_page, odd_page section breaks.
- [ ] Verify per-section page size/orientation/margins.
- [ ] Verify per-section header/footer swaps.
- [ ] Verify page breaks and consecutive page breaks.

## Headers and footers

- [ ] Verify left/center/right header placement.
- [ ] Verify left/center/right footer placement.
- [ ] Verify `{page}`, `{total}`, `{title}` expansion.
- [ ] Verify default footer.
- [ ] Verify `footer={}` disables footer.
- [ ] Verify page totals after TOC, section breaks, and page breaks.

## Styles

- [ ] Verify global `style_overrides` for Heading 1 and all documented built-in styles.
- [ ] Verify custom style creation and inheritance from `base`.
- [ ] Verify paragraph references to custom styles.
- [ ] Verify conflicting global/per-section/custom-style precedence.
- [ ] Verify style behavior across tables, captions, quotes, and headings.

## Metadata and artifact

- [ ] Verify `author`, `subject`, and `keywords` appear in Word document metadata.
- [ ] Verify generated file is a valid `.docx` and opens in standard Word readers.
- [ ] Verify output response shape and artifact/file reference.
- [ ] Verify `system.files` receives the generated output via `{{nodeOutput}}`.
- [ ] Verify deterministic behavior for identical inputs where practical.

## Error handling

- [ ] Test malformed section content.
- [ ] Test invalid enum values for section types, list styles, page sizes, orientations, and break types.
- [ ] Test null/empty values.
- [ ] Test very large text and presentations/documents.
- [ ] Capture exact success and error payloads.

## Evidence promotion rule

A verification item moves into confirmed evidence only after a concrete runtime observation, reproducible test, or authoritative product documentation establishes the behavior. Once confirmed, remove that item from this queue and record the result and test context in the relevant evidence file.