# Evidence Record: Export HTML V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-HTML-V2-001  
**Date:** 2026-08-23  
**Capability:** Self-contained HTML export for dashboards, reports, summaries, structured data, charts, and rich visual pages.  
**Source:** User-supplied Export HTML V2 tool configuration JSON.

## Tool configuration

- Tool name: `export_html_v2`
- Tool title: `Export HTML V2`
- Enabled: `true`
- Tool type: `mcp`
- System/export classification: system tool, export tool
- `returnDirect`: `false`
- Output is appended to `system.files` via `{{nodeOutput}}`.
- Required top-level inputs: `title`, `sections`.
- Optional top-level inputs: `theme`, `accent_color`.

## What was observed

1. The tool generates a polished, self-contained HTML page from structured content.
2. `title` is used as the output filename and the supplied contract restricts it to letters, numbers, spaces, and hyphens.
3. The generated page uses Google Fonts, CSS custom properties, and Chart.js through a CDN.
4. The contract explicitly instructs the model to call the tool proactively when rich visual output would be more useful, including dashboards, reports, summaries, data visualization, structured data, and requests to make content visually attractive.
5. Each section contains `type` and `content`.
6. Supported section types are `hero`, `section`, `text`, `bullet_list`, `stat_cards`, `table`, `chart`, `callout`, `code_block`, `timeline`, `two_column`, `image`, and `divider`.
7. `hero` supports title, subtitle, badge, and metadata tags and always renders at the top regardless of its list position.
8. `section` supports heading, optional text, and heading levels 1 through 3.
9. `bullet_list` supports an optional heading and an item array.
10. `stat_cards` supports value, label, optional change, and `positive`, `negative`, or `neutral` change types.
11. `table` supports heading, headers, and rows and is described as striped with hover highlighting.
12. Charts use Chart.js and support bar, column, line, pie, and doughnut types. Bar is horizontal and column is vertical. Multiple series are supported.
13. Callouts support `info`, `warning`, `success`, `tip`, and `error` variants and an optional title.
14. Code blocks render with dark code-block styling and macOS-style window chrome; language is shown as a badge when supplied.
15. Timelines contain ordered steps, each with title and optional text.
16. Two-column sections contain left and right panels, each supporting heading, text, and item lists.
17. Images support a URL or data URI plus optional alt text and caption.
18. Themes are `light` (default), `dark`, and `corporate`.
19. `accent_color` can override the theme accent/gradient color and is intended for branded output.
20. The stated document limit is 200 sections.
21. The configuration includes the complete system tool identifier and module/server metadata, but those identifiers do not independently establish runtime implementation behavior.

## What this evidence proves

The supplied configuration establishes the documented input shape, section taxonomy, theme options, accent-color override, proactive-use guidance, chart capabilities, filename restriction, section limit, enabled state, and `system.files` artifact wiring.

## What this evidence does not prove

The configuration does not establish actual visual fidelity, responsive behavior, browser compatibility, CDN availability behavior, Chart.js loading failures, Google Fonts loading failures, image fetch behavior, malformed chart handling, accessibility semantics, generated HTML sanitization, XSS protections, output file persistence, exact artifact response shape, deterministic rendering, or practical payload/DOM limits beyond the documented 200-section limit.

## Verification

Runtime verification is pending and belongs in `04-Verification/Export-HTML-V2-Verification.md`.