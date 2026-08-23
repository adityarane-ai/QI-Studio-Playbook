# Evidence Record: Export Word V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-WORD-V2-001  
**Date:** 2026-08-23  
**Capability:** Word export from structured sections, document formatting, tables, images, native charts, hyperlinks, section breaks, TOC, bookmarks, cross-references, footnotes, styles, and custom styles.  
**Source:** User-supplied Export Word V2 tool configuration JSON.

## Tool configuration

- Tool name: `export_word_v2`
- Tool title: `Export Word V2`
- Enabled: `true`
- Tool type: `mcp`
- System/export classification: system tool, export tool
- `returnDirect`: `false`
- Output is appended to `system.files` via `{{nodeOutput}}`.
- Required top-level inputs: `title`, `sections`.
- Optional top-level inputs: `page_size`, `orientation`, `margins`, `cover_page`, `header`, `footer`, `author`, `subject`, `keywords`, `style_overrides`, `custom_styles`, `update_fields_on_open`.

## What was observed

1. The tool generates a polished Word `.docx` document from structured sections.
2. `title` is used as the output filename and the supplied contract restricts it to letters, numbers, spaces, and hyphens.
3. Each section contains `type` and `content`.
4. Supported section types are `cover`, `toc`, `heading`, `paragraph`, `bullet_list`, `numbered_list`, `table`, `image`, `chart`, `hyperlink`, `page_break`, `section_break`, `horizontal_rule`, `key_value`, `callout`, `footnote_ref`, `bookmark`, and `cross_reference`.
5. Heading levels 1 through 6 map to Word built-in Heading 1 through Heading 6 styles.
6. TOC supports levels 1 through 6 and an optional `hyperlink` flag; the contract states the TOC is injected as a Word field and evaluated when the document opens.
7. `update_fields_on_open` defaults to `true` and controls automatic Word field updating on open.
8. Paragraphs support style, alignment, font family, font size, font color, bold, italic, underline, strikethrough, highlight, spacing, and line spacing.
9. Bullet lists support flat strings or structured items with optional levels/children, and styles `bullet`, `dash`, and `circle`.
10. Numbered lists support `start` and styles `decimal`, `upper-roman`, `lower-roman`, `upper-letter`, and `lower-letter`.
11. Tables support optional headers, rows containing scalar strings or cell dictionaries, column widths, header styling, alternating rows, borders, and cell `rowspan`/`colspan` merging.
12. Table header rows automatically repeat when a table spans pages.
13. Column widths below 100 are interpreted as inches and widths of 100 or greater as points.
14. Cells can carry value, rowspan, colspan, bold, italic, alignment, background color, and font color.
15. Images support HTTP(S), local file paths, and inline data URIs; images are inline only in v1.
16. Image width/height values below 100 are interpreted as inches and values of 100 or greater as points. When both are omitted, images scale to approximately 6 inches wide.
17. Native Word charts are editable in Word. Supported chart types are bar, column, line, and pie; pie uses only the first series.
18. Standalone hyperlinks are supported, and inline markdown-style links are also documented.
19. Section breaks support `next_page`, `continuous`, `even_page`, and `odd_page` and can change page size, orientation, margins, headers, and footers mid-document.
20. Headers and footers support left, center, right text and `{page}`, `{total}`, `{title}` macros. The default footer is centered `Page {page}`; `footer={}` disables it.
21. Bookmarks can be attached to headings or created with a bookmark section, and cross-reference sections emit Word REF fields pointing to bookmark names.
22. Footnote references use native Word footnotes and are rendered at the page bottom with Word-managed numbering.
23. Inline styling supports `<b>`, `<i>`, `<u>`, `<s>`, `<color value="...">`, `<br/>`, and markdown-style hyperlinks.
24. Global style overrides support built-in styles including Title, Subtitle, Body, Caption, Code, Quote, Intense Quote, and Heading 1 through Heading 6.
25. Custom reusable styles can be defined with a name, type, base style, and formatting properties, then referenced by paragraphs.
26. Supported page sizes are letter, A4, legal, and tabloid. Default orientation is portrait. Margins are expressed in points.
27. The stated limits are 500 sections, 2000 table rows, 10 MB per image, and a 15-second image fetch timeout.
28. The tool configuration contains a module/server identifier and tool identifier, but these identifiers are operational metadata rather than evidence of runtime semantics.

## What this evidence proves

The supplied configuration establishes the currently documented input contract, section types, formatting options, table/image/chart behavior, Word-native feature intent, page configuration, style controls, limits, enabled state, and artifact wiring for Export Word V2.

## What this evidence does not prove

The configuration alone does not prove actual `.docx` rendering fidelity, Word compatibility/version differences, native chart fidelity, table pagination edge cases, cell merge behavior in difficult layouts, image download behavior, malformed-input handling, TOC population behavior across Word clients, bookmark/reference correctness, footnote placement edge cases, style precedence, custom-style inheritance, exact error payloads, output metadata, deterministic output, or file persistence beyond the declared `system.files` variable update.

## Verification

Runtime verification is pending and belongs in `04-Verification/Export-Word-V2-Verification.md`.