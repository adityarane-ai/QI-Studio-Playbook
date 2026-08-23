# Verification Queue: Export HTML V2

**Capability:** `export_html_v2`  
**Evidence date:** 2026-08-23  
**Status:** Configuration captured; runtime verification pending.

## Input and filename

- [ ] Verify minimal valid HTML generation.
- [ ] Verify `title` becomes the output filename.
- [ ] Verify allowed filename characters.
- [ ] Verify invalid filename behavior.
- [ ] Verify missing `title` or `sections` behavior.
- [ ] Verify 199, 200, and 201 section boundaries.

## Section rendering

- [ ] Verify hero renders at the top regardless of section position.
- [ ] Verify section heading/text/level.
- [ ] Verify plain text.
- [ ] Verify bullet list and optional heading.
- [ ] Verify stat cards, optional changes, and positive/negative/neutral variants.
- [ ] Verify styled tables, striped rows, and hover behavior.
- [ ] Verify callout variants info/warning/success/tip/error and optional titles.
- [ ] Verify code blocks, language badge, and code preservation.
- [ ] Verify timeline ordering, title, and optional text.
- [ ] Verify two-column layouts and all panel fields.
- [ ] Verify images from URLs and data URIs, alt text, and captions.
- [ ] Verify divider rendering.
- [ ] Verify unknown section types fail clearly.
- [ ] Verify missing/extra fields behave consistently.

## Charts

- [ ] Verify bar, column, line, pie, and doughnut charts render.
- [ ] Verify chart labels and one/multiple series.
- [ ] Verify non-numeric values.
- [ ] Verify mismatched labels and value lengths.
- [ ] Verify empty series/labels behavior.
- [ ] Verify chart interaction and tooltips in browser.
- [ ] Verify chart rendering when CDN is unavailable.
- [ ] Verify charts remain usable with large datasets.

## Themes and branding

- [ ] Verify light, dark, and corporate themes.
- [ ] Verify default theme is light.
- [ ] Verify `accent_color` override affects intended accent/gradient elements.
- [ ] Verify invalid color strings.
- [ ] Verify theme and accent combination precedence.
- [ ] Verify GEP-branded accent use through the documented override.

## External dependencies and security

- [ ] Verify Google Fonts loading and fallback when unavailable.
- [ ] Verify Chart.js CDN loading and failure behavior.
- [ ] Verify HTML is self-contained apart from declared external fonts/charts.
- [ ] Verify user-provided text is safely escaped.
- [ ] Verify URLs and images cannot introduce unintended script execution.
- [ ] Verify code blocks do not execute embedded HTML/script.

## Output and artifact

- [ ] Capture exact success response shape.
- [ ] Verify generated HTML artifact/path/reference.
- [ ] Verify `system.files` receives `{{nodeOutput}}`.
- [ ] Verify output can be opened in modern browsers.
- [ ] Verify responsive behavior on narrow and wide viewports.
- [ ] Verify print behavior where relevant.
- [ ] Verify deterministic output for identical inputs where practical.

## Evidence promotion rule

A verification item moves into confirmed evidence only after runtime observation, a reproducible test result, or authoritative product documentation establishes the behavior. Remove confirmed items from this queue and record the result and test context in the evidence file.