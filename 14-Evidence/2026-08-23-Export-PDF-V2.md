# Evidence Record: Export PDF V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-PDF-V2-001  
**Date:** 2026-08-23  
**Capability:** PDF export from structured sections, document structure, formatting, images, charts, pagination, page configuration, cover pages, headers/footers, table of contents, endnotes, and style overrides.  
**Source:** User-supplied Export PDF V2 tool configuration text.

## What was observed

1. The tool generates a polished PDF document from a structured list of sections.
2. The `title` parameter is used as the output filename and is restricted to letters, numbers, spaces, and hyphens.
3. Each section contains `type` and `content` fields.
4. Supported section types are `cover`, `toc`, `heading`, `paragraph`, `bullet_list`, `numbered_list`, `table`, `image`, `chart`, `spacer`, `horizontal_rule`, `page_break`, `key_value`, `callout`, `endnote_ref`, and `endnotes`.
5. Cover sections support title, optional subtitle, author, date, and logo.
6. The top-level `cover_page` parameter can also expose a cover page.
7. TOC sections support configurable levels 1, 2, or 3 and populate automatically from heading sections.
8. Heading levels are 1, 2, or 3, and an individual heading can be excluded from the TOC with `toc:false`.
9. Paragraphs support optional style, alignment, font size, font color, bold, and italic controls.
10. Bullet lists support `bullet`, `dash`, and `circle` styles.
11. Numbered lists support a configurable starting number.
12. Tables support optional headers, rows, column widths, header styling, and alternating-row striping.
13. Tables automatically paginate and repeat the header row on each page.
14. Images accept direct HTTP(S) image URLs, local file paths, and inline `data:image/png;base64,...` URIs.
15. Remote image retrieval has a 15-second timeout and a 10 MB maximum image size.
16. The supplied contract explicitly warns that HTML pages are not valid image sources and that image URLs must point directly to image binaries.
17. Wikimedia images must use direct `upload.wikimedia.org` image URLs rather than article-page URLs.
18. Missing image dimensions cause the image to scale to usable page width; one supplied dimension preserves aspect ratio.
19. Charts support bar, column, line, and pie types.
20. Pie charts use only the first series.
21. Page configuration supports letter, A4, legal, and tabloid sizes.
22. Page orientation supports portrait and landscape.
23. Margins are configurable in points.
24. A cover may be added as a top-level `cover_page` or as a `cover` section.
25. Headers and footers support left, center, and right text and macros `{page}`, `{total}`, and `{title}`.
26. The default footer is centered `Page {page}` unless an explicit footer is supplied; `footer={}` disables it.
27. Endnote references are numbered in body order and are flushed at the next `endnotes` section or automatically appended at the end when no endnotes section is provided.
28. Inline text styling supports `<b>`, `<i>`, `<u>`, `<font color="...">`, `<super>`, `<sub>`, and `<br/>`.
29. The default body font is DejaVu Sans.
30. Chinese, Japanese, and Korean characters are explicitly not supported and render as blank glyphs; transliteration or translation is required before tool invocation.
31. Document-wide style overrides can recolor named styles including `Title`, `Subtitle`, `Heading1`, `Heading2`, `Heading3`, `Body`, `Caption`, `TableCell`, `TableHeader`, and `Code`.
32. Per-section overrides are available for paragraph and heading sections.
33. The stated document limit is 500 sections.
34. The stated table limit is 2000 rows per table.
35. The stated image limit is 10 MB per image with a 15-second fetch timeout.

## What this evidence proves

This supplied contract establishes the currently documented input shapes, supported section types, formatting features, image rules, chart types, page settings, cover/header/footer/TOC/endnote behavior, style controls, Unicode support limitations, and stated limits for the Export PDF V2 tool.

## What this evidence does not prove

The configuration text alone does not establish exact runtime behavior for PDF rendering fidelity, font substitution beyond the stated default, pagination edge cases, table width overflow behavior, chart rendering fidelity, unsupported URL handling details beyond the described failure message, image aspect-ratio behavior under conflicting dimensions, TOC ordering under nested headings, endnote placement across page breaks, macro expansion edge cases, style precedence between global and per-section overrides, color conversion, PDF metadata, accessibility/tagging, selectable text, embedded font behavior, or exact error/status payloads.

## Security and reliability notes

- Do not fabricate image URLs. The tool contract explicitly says unverifiable image URLs should be omitted rather than invented.
- Remote image fetching introduces external network dependency, timeout, and size-limit failure modes.
- CJK content requires transformation before export because the documented default font does not support CJK glyphs in this tool.
- The `title` filename restriction should be enforced before invocation.

## Verification queue

Runtime behavior should be tested against the companion verification file in `04-Verification/Export-PDF-V2-Verification.md`.
