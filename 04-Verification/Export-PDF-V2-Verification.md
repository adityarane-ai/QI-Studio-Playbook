# Verification Queue: Export PDF V2

**Capability:** `export_pdf_v2`  
**Status:** Configuration/evidence captured; runtime verification pending.

## Input and basic output

- [ ] Verify a minimal single-section PDF is generated successfully.
- [ ] Verify `title` becomes the output filename.
- [ ] Verify allowed filename characters: letters, numbers, spaces, hyphens.
- [ ] Verify rejection/error behavior for disallowed filename characters such as `/`, `:`, `?`, `*`, and `#`.
- [ ] Verify missing or invalid `sections` input produces a clear validation error.
- [ ] Verify the 500-section limit and behavior at 499, 500, and 501 sections.

## Section dispatch and schemas

- [ ] Verify every documented section type renders: `cover`, `toc`, `heading`, `paragraph`, `bullet_list`, `numbered_list`, `table`, `image`, `chart`, `spacer`, `horizontal_rule`, `page_break`, `key_value`, `callout`, `endnote_ref`, and `endnotes`.
- [ ] Verify an unknown section type fails cleanly rather than silently disappearing.
- [ ] Verify missing `type` or `content` is rejected clearly.
- [ ] Verify extra unknown fields are ignored or rejected consistently and document the observed behavior.

## Cover pages

- [ ] Verify top-level `cover_page` renders before all sections.
- [ ] Verify an inline `cover` section renders at its requested position.
- [ ] Verify optional cover subtitle, author, date, and logo individually and in combination.
- [ ] Verify differences between top-level cover and inline cover behavior, especially with TOC placement.

## Table of contents

- [ ] Verify `toc` is populated automatically from heading sections.
- [ ] Verify TOC levels 1, 2, and 3.
- [ ] Verify `heading.toc=false` excludes only that heading.
- [ ] Verify heading hierarchy and numbering/page references are correct after pagination.
- [ ] Verify TOC behavior when there are no headings.
- [ ] Verify TOC placement when headings occur before and after the TOC section.

## Headings and text

- [ ] Verify heading levels 1, 2, and 3 render with correct hierarchy.
- [ ] Verify paragraph alignment options.
- [ ] Verify paragraph `font_size`, `font_color`, `bold`, `italic`, and named `style` overrides.
- [ ] Verify inline `<b>`, `<i>`, `<u>`, `<font color>`, `<super>`, `<sub>`, and `<br/>` rendering.
- [ ] Verify malformed or unclosed inline tags fail safely or render predictably.
- [ ] Verify mixed inline styling on a single paragraph.
- [ ] Verify long paragraphs wrap correctly across page boundaries.

## Lists

- [ ] Verify bullet-list styles `bullet`, `dash`, and `circle`.
- [ ] Verify numbered lists with default start.
- [ ] Verify numbered lists with explicit non-1 `start` values.
- [ ] Verify multiline list items and long list wrapping.
- [ ] Verify lists across page breaks.

## Tables

- [ ] Verify tables with headers and normal rows.
- [ ] Verify tables without headers.
- [ ] Verify `col_widths` are honored in points.
- [ ] Verify `header_style.bg_color` overrides the default header background.
- [ ] Verify `alternating_rows=true` produces zebra striping.
- [ ] Verify a table auto-paginates correctly.
- [ ] Verify the header row repeats on every continuation page.
- [ ] Verify wide tables under portrait orientation and document the overflow behavior.
- [ ] Verify wide tables under landscape orientation.
- [ ] Verify heterogeneous cell values, including text, integers, floats, booleans, dates, and nulls.
- [ ] Verify 1999, 2000, and 2001 table-row boundaries.
- [ ] Verify very long cell content and wrapping behavior.
- [ ] Verify explicit column widths that exceed usable page width.
- [ ] Verify empty tables and zero-row tables.

## Images

- [ ] Verify a valid local image path renders.
- [ ] Verify a valid direct HTTP image URL renders.
- [ ] Verify a valid direct HTTPS image URL renders.
- [ ] Verify an inline `data:image/png;base64,...` URI renders.
- [ ] Verify an HTML page URL is rejected/rendered as an unavailable image with the documented failure text.
- [ ] Verify a nonexistent URL produces a useful failure reason.
- [ ] Verify a remote image timing out at approximately the 15-second limit fails predictably.
- [ ] Verify an image over 10 MB is rejected or marked unavailable predictably.
- [ ] Verify width+height are both honored.
- [ ] Verify width-only preserves aspect ratio.
- [ ] Verify height-only preserves aspect ratio.
- [ ] Verify both dimensions omitted scales to usable page width.
- [ ] Verify image alignment values.
- [ ] Verify image captions and caption wrapping.
- [ ] Verify direct Wikimedia binary URLs work and article-page URLs fail as documented.

## Charts

- [ ] Verify bar chart rendering.
- [ ] Verify column chart rendering.
- [ ] Verify line chart rendering.
- [ ] Verify pie chart rendering.
- [ ] Verify chart title.
- [ ] Verify chart labels and one or multiple series.
- [ ] Verify pie charts use only the first series as documented.
- [ ] Verify explicit chart width and height.
- [ ] Verify negative values, zero values, and decimal values.
- [ ] Verify mismatched label/series lengths and document validation behavior.
- [ ] Verify charts spanning page boundaries.
- [ ] Compare PDF chart appearance against the same data exported through related tools where useful.

## Layout and page configuration

- [ ] Verify `letter`, `a4`, `legal`, and `tabloid` page sizes.
- [ ] Verify portrait orientation.
- [ ] Verify landscape orientation.
- [ ] Verify top, bottom, left, and right margins in points.
- [ ] Verify extreme but valid margin values and document minimum/maximum behavior.
- [ ] Verify `spacer` heights in points.
- [ ] Verify `horizontal_rule` color and thickness.
- [ ] Verify `page_break` starts a new page exactly once.
- [ ] Verify multiple consecutive page breaks.
- [ ] Verify section flow after page breaks, covers, and TOCs.

## Headers and footers

- [ ] Verify header left/center/right placement behavior using the documented shape.
- [ ] Verify footer left/center/right placement behavior where supported by the structure.
- [ ] Verify `{page}` macro.
- [ ] Verify `{total}` macro.
- [ ] Verify `{title}` macro.
- [ ] Verify default centered `Page {page}` footer.
- [ ] Verify `footer={}` disables the footer completely.
- [ ] Verify page and total counts remain correct after page breaks and TOC generation.

## Key-value blocks and callouts

- [ ] Verify `key_value` renders as a two-column metadata block.
- [ ] Verify `label_width` affects the label column as documented.
- [ ] Verify long labels and long values wrap correctly.
- [ ] Verify callout variants `info`, `warning`, and `success`.
- [ ] Verify optional callout title.
- [ ] Verify long callout text across page boundaries.

## Endnotes

- [ ] Verify `endnote_ref` creates numbered references in body order.
- [ ] Verify matching note bodies appear in the next `endnotes` section.
- [ ] Verify multiple endnote references to the same or different notes.
- [ ] Verify `endnotes` title override.
- [ ] Verify automatic endnote append when no `endnotes` section exists.
- [ ] Verify endnotes after a page break.

## Unicode and fonts

- [ ] Verify Latin Extended characters.
- [ ] Verify Greek, Cyrillic, Hebrew, Arabic, IPA, math operators, currencies, arrows, and geometric symbols described as supported.
- [ ] Verify CJK characters render as blank glyphs as documented, then confirm transliteration is an effective mitigation.
- [ ] Verify DejaVu Sans is the actual default embedded/used font in output PDF where inspectable.
- [ ] Verify font fallback/substitution behavior for unsupported glyphs outside the documented supported range.

## Style overrides and precedence

- [ ] Verify global `style_overrides` for Heading1.
- [ ] Verify global overrides for other named styles.
- [ ] Verify per-section overrides on headings and paragraphs.
- [ ] Test conflicting global vs per-section settings and document precedence.
- [ ] Test style override behavior across tables, captions, and other section types.

## PDF artifact integrity

- [ ] Verify the generated file is a valid PDF and can be opened by standard PDF readers.
- [ ] Verify page count matches expected pagination.
- [ ] Verify text is selectable/searchable where expected.
- [ ] Verify hyperlinks, if any are introduced by supported content mechanisms, behave correctly.
- [ ] Inspect whether fonts are embedded and whether glyph mapping is preserved.
- [ ] Inspect PDF metadata and document properties.
- [ ] Inspect whether output is tagged/accessibility-enabled and document the result.
- [ ] Verify deterministic rendering for identical inputs where practical.
- [ ] Verify repeated runs do not corrupt or overwrite unrelated files.

## Error handling and edge cases

- [ ] Test malformed section content.
- [ ] Test missing required fields within each section type.
- [ ] Test invalid enum values for section types, heading levels, list styles, chart types, page sizes, and orientations.
- [ ] Test null/empty strings and empty arrays in supported fields.
- [ ] Test very large text payloads and document any practical rendering limits.
- [ ] Test simultaneous use of many advanced features in one document.
- [ ] Capture exact error messages and output behavior for all rejected inputs.

## Evidence promotion rule

A verification item moves from this queue into the evidence/confirmed area only after a concrete runtime observation, reproducible test result, or authoritative product documentation establishes the behavior. Once confirmed, remove the corresponding item from this queue and record the evidence and test context in the relevant evidence file.
