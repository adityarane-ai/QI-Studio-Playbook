# Evidence Record: Export PowerPoint V2

**Evidence ID:** QI-OBS-2026-08-23-EXPORT-PPT-V2-001  
**Date:** 2026-08-23  
**Capability:** Agent export tool for branded PowerPoint presentations.  
**Source:** User-supplied QI Studio tool configuration JSON and tool description.

## What was observed

The `Export PowerPoint V2` tool generates a branded PowerPoint `.pptx` presentation from structured content.

### Tool identity and runtime configuration observed

- Tool name: `export_powerpoint_v2`
- Display title: `Export PowerPoint V2`
- Tool type: `mcp`
- Enabled: `true`
- System/export tool metadata identifies it as an export capability.
- The configured input schema requires `title` and `slides`.
- `template` is optional and defaults to `default` in the tool schema.
- The configured tool is set to `returnDirect: false`.
- The tool's configured variable update appends `{{nodeOutput}}` to `system.files`.

### Output filename

The `title` parameter is used as the output filename. The supplied configuration says to use only letters, numbers, spaces, and hyphens.

### Tool schema

Observed top-level input schema:

- `title`: string, required.
- `slides`: array, required.
- `template`: string, optional, default `default`.

The supplied schema leaves `slides.items` unconstrained at the JSON-Schema level, with the detailed slide contract defined in the human-readable tool description.

### Slide structure

Each slide requires exactly two top-level keys:

- `layout`: string containing one of the supported layout names for the chosen template.
- `content`: object whose fields depend on the selected layout.

### Supported layout content shapes

| Layout | Content shape |
|---|---|
| `title_slide` | `{ title, subtitle }` |
| `section_header` | `{ title }` |
| `title_subtitle_and_content` | `{ title, subtitle, bullets: [str] }` |
| `title_and_content` | `{ title, bullets: [str] }` |
| `two_content` | `{ title, left: { heading, bullets }, right: { heading, bullets } }` |
| `two_content_with_headers` | `{ title, left: { heading, bullets }, right: { heading, bullets } }` |
| `three_content` | `{ title, left: { heading, bullets }, center: { heading, bullets }, right: { heading, bullets } }` |
| `title_and_picture` | `{ title, image: str }` where image is a file path or URL |
| `title_and_chart` | `{ title, chart: { type, labels, series: [{name, values}] } }` |
| `title_content_and_chart` | `{ title, bullets: [str], chart: { type, labels, series: [{name, values}] } }` |
| `title_content_and_picture` | `{ title, bullets: [str], image: str }` where image is a file path or URL |

### Layout selection guidance

- Use `two_content` when each column is a single text area.
- Use `two_content_with_headers` when each column needs a distinct heading above its content.
- Use `title_subtitle_and_content` when a visible subheading or category label is required below the main title and above bullets.
- Use `title_and_chart` when the chart fills the slide.
- Use `title_content_and_chart` when bullets accompany the chart.
- Use `title_and_picture` when the image fills the slide.
- Use `title_content_and_picture` when bullets accompany the image.

### Chart types

Supported chart types:

- `bar`
- `bar_stacked`
- `column`
- `line`
- `pie`

Chart series shape:

```text
[{"name": "Revenue", "values": [100, 200, 150]}]
```

The chart object is structured as:

```text
{
  "type": "column",
  "labels": ["Q1", "Q2", "Q3"],
  "series": [
    {"name": "Revenue", "values": [100, 200, 150]}
  ]
}
```

### Templates

Available templates and stated intended use:

| Template | Description / use |
|---|---|
| `default` | Dark professional blue/black; used when no preference is given. |
| `gep` | GEP branded purple; use for internal GEP presentations. |
| `business_blue_black` | Dark professional blue/black; same as default. |

### Template-specific layout support

The supplied configuration explicitly requires checking supported layouts before selecting layouts. Unsupported layouts must not be used because the system may attempt a fallback and slide content may be lost or rendered incorrectly.

#### `default` / `gep`

**Supported:**

- `title_slide`
- `section_header`
- `title_subtitle_and_content`
- `title_and_content`
- `two_content`
- `three_content`
- `title_and_picture`
- `title_and_chart`
- `title_content_and_chart`
- `title_content_and_picture`

**Unsupported:**

- `two_content_with_headers`

#### `business_blue_black`

**Supported:**

- `title_slide`
- `section_header`
- `title_and_content`
- `two_content`
- `two_content_with_headers`
- `title_and_picture`
- `title_and_chart`
- `title_content_and_chart`
- `title_content_and_picture`

**Unsupported:**

- `title_subtitle_and_content`
- `three_content`

## Minimal example

```text
{
  "title": "Quarterly Review",
  "template": "default",
  "slides": [
    {
      "layout": "title_slide",
      "content": {
        "title": "Quarterly Review",
        "subtitle": "Q2 performance"
      }
    },
    {
      "layout": "title_and_content",
      "content": {
        "title": "Highlights",
        "bullets": [
          "Revenue increased",
          "Pipeline strengthened"
        ]
      }
    }
  ]
}
```

## Evidence status

**Status:** Configuration contract captured from supplied tool description and JSON configuration.

This evidence establishes the listed layouts, content shapes, chart types, template names, filename restriction, template-specific support matrix, top-level schema, enabled state, returnDirect setting, and configured `system.files` output-variable update as *configured/documented behavior*. It does not establish that every configured behavior occurs identically at runtime.

## What this evidence does not prove

The supplied configuration does not establish exact runtime behavior for:

- generated slide visual fidelity against the template,
- font substitution or unavailable-font handling,
- image URL downloading, authentication, or failure behavior,
- image file size/type limits,
- chart rendering and axis/legend formatting,
- malformed chart series handling,
- bullet length limits and text overflow behavior,
- unsupported-layout fallback behavior in practice,
- duplicate or invalid slide layout handling,
- exact success output payload structure,
- file persistence/path/reference details after the `system.files` append,
- error payload structure.

## Related verification

See `04-Verification/Export-PowerPoint-V2-Verification.md`.
