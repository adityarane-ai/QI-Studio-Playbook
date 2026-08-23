# Evidence Record: Export PDF V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-PDF-V2-001  
**Date:** 2026-08-23  
**Capability:** PDF export from structured sections, document structure, formatting, images, charts, pagination, page configuration, cover pages, headers/footers, table of contents, endnotes, and style overrides.  
**Source:** User-supplied Export PDF V2 tool configuration JSON and tool description.

## What was observed

1. The tool generates a polished PDF document from a structured list of sections.
2. Tool name: `export_pdf_v2`.
3. Display title: `Export PDF V2`.
4. The tool is configured as an MCP system export tool and is enabled.
5. The configured JSON schema requires `title` and `sections`.
6. Optional top-level inputs include `page_size`, `orientation`, `margins`, `cover_page`, `header`, `footer`, `author`, and `style_overrides`.
7. `page_size` defaults to `letter` and `orientation` defaults to `portrait`.
8. `author` defaults to an empty string and the other optional structured inputs default to null where shown in the supplied schema.
9. The tool is configured with `returnDirect: false`.
10. The configured variable update appends `{{nodeOutput}}` to `system.files`.
11. The `title` parameter is used as the output filename and is restricted to letters, numbers, spaces, and hyphens.
12. Each section contains `type` and `content` fields.
13. Supported section types are `cover`, `toc`, `heading`, `paragraph`, `bullet_list`, `numbered_list`, `table`, `image`, `chart`, `spacer`, `horizontal_rule`, `page_break`, `key_value`, `callout`, `endnote_ref`, and `endnotes`.
14. Cover sections support title, optional subtitle, author, date, and logo.
15. The top-level `cover_page` parameter can also expose a cover page.
16. TOC sections support configurable levels 1, 2, or 3 and populate automatically from heading sections.
17. Heading levels are 1, 2, or 3, and an individual heading can be excluded from the TOC with `toc:false`.
18. Paragraphs support optional style, alignment, font size, font color, bold, and italic controls.
19. Bullet lists support `bullet`, `dash`, and `circle` styles.
20. Numbered lists support a configurable starting number.
21. Tables support optional headers, rows, column widths, header styling, and alternating-row striping.
22. Tables automatically paginate and repeat the header row on each page.
23. Images accept direct HTTP(S) image URLs, local file paths, and inline `data:image/png;base64,...` URIs.
24. Remote image retrieval has a 15-second timeout and a 10 MB maximum image size.
25. The supplied contract explicitly warns that HTML pages are not valid image sources and that image URLs must point directly to image binaries.
26. Wikimedia images must use direct `upload.wikimedia.org` image URLs rather than article-page URLs.
27. Missing image dimensions cause the image to scale to usable page width; one supplied dimension preserves aspect ratio.
28. Charts support bar, column, line, and pie types.
29. Pie charts use only the first series.
30. Page configuration supports letter, A4, legal, and tabloid sizes.
31. Page orientation supports portrait and landscape.
32. Margins are configurable in points.
33. A cover may be added as a top-level `cover_page` or as a `cover` section.
34. Headers and footers support left, center, and right text and macros `{page}`, `{total}`, and `{title}`.
35. The default footer is centered `Page {page}` unless an explicit footer is supplied; `footer={}` disables it.
36. Endnote references are numbered in body order and are flushed at the next `endnotes` section or automatically appended at the end when no `endnotes` section is provided.
37. Inline text styling supports `<b>`, `<i>`, `<u>`, `<font color="...">`, `<super>`, `<sub>`, and `<br/>`.
38. The default body font is DejaVu Sans.
39. Chinese, Japanese, and Korean characters are explicitly not supported and render as blank glyphs; transliteration or translation is required before tool invocation.
40. Document-wide style overrides can recolor named styles including `Title`, `Subtitle`, `Heading1`, `Heading2`, `Heading3`, `Body`, `Caption`, `TableCell`, `TableHeader`, and `Code`.
41. Per-section overrides are available for paragraph and heading sections.
42. The stated document limit is 500 sections.
43. The stated table limit is 2000 rows per table.
44. The stated image limit is 10 MB per image with a 15-second fetch timeout.

## What this evidence proves

This supplied contract establishes the currently documented input shapes, configured schema/defaults, supported section types, formatting features, image rules, chart types, page settings, cover/header/footer/TOC/endnote behavior, style controls, Unicode support limitations, output-variable wiring, and stated limits for the Export PDF V2 tool.

## What this evidence does not prove

The configuration text alone does not establish exact runtime behavior for PDF rendering fidelity, font substitution beyond the stated default, pagination edge cases, table width overflow behavior, chart rendering fidelity, unsupported URL handling details beyond the described failure message, image aspect-ratio behavior under conflicting dimensions, TOC ordering under nested headings, endnote placement across page breaks, macro expansion edge cases, style precedence between global and per-section overrides, color conversion, PDF metadata, accessibility/tagging, selectable text, embedded font behavior, exact success output/reference structure, exact error/status payloads, or actual persistence behavior of the `system.files` append.

## Security and reliability notes

- Do not fabricate image URLs. The tool contract explicitly says unverifiable image URLs should be omitted rather than invented.
- Remote image fetching introduces external network dependency, timeout, and size-limit failure modes.
- CJK content requires transformation before export because the documented default font does not support CJK glyphs in this tool.
- The `title` filename restriction should be enforced before invocation.

## Verification queue

Runtime behavior should be tested against the companion verification file in `04-Verification/Export-PDF-V2-Verification.md`.
