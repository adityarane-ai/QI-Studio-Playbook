# Verification Queue: Export PowerPoint V2

**Capability:** `export_powerpoint_v2`  
**Evidence date:** 2026-08-23  
**Source:** Supplied QI Studio tool configuration text.  
**Status:** Configuration captured; runtime behavior pending verification.

## Verification items

### Input and filename

1. Verify `title` is actually used as the output filename.
2. Verify filename validation accepts letters, numbers, spaces, and hyphens.
3. Verify filenames containing unsupported characters are rejected or sanitized deterministically.
4. Verify omitted/empty `title` behavior.

### Slides and layouts

5. Verify every slide requires `layout` and `content`.
6. Verify each listed layout accepts exactly its documented content shape.
7. Verify missing required content fields produce a structured error rather than silent content loss.
8. Verify extra fields are ignored, rejected, or preserved predictably.
9. Verify slide order matches input order.
10. Verify empty `bullets` arrays render without malformed layout behavior.

### Templates and supported-layout matrix

11. Verify `default` accepts exactly the ten documented supported layouts.
12. Verify `gep` accepts the same supported layout set as `default`.
13. Verify `business_blue_black` accepts its documented supported layouts.
14. Verify unsupported layouts are rejected or fall back in a deterministic way.
15. Verify unsupported-layout fallback does not silently lose content.
16. Verify omitted template defaults to `default`.
17. Verify unknown template names produce a clear validation error.

### Charts

18. Verify `bar`, `bar_stacked`, `column`, `line`, and `pie` charts render correctly.
19. Verify `labels` length must match each series' `values` length, or document actual mismatch handling.
20. Verify multiple chart series render with correct names and values.
21. Verify empty labels or values handling.
22. Verify non-numeric chart values are rejected or coerced predictably.
23. Verify stacked bar behavior is materially different from normal bar behavior.
24. Verify chart title/legend/axis formatting defaults and any clipping or overflow behavior.

### Pictures

25. Verify local file paths are supported by `image` fields.
26. Verify HTTP/HTTPS URLs are supported.
27. Verify invalid or inaccessible image paths/URLs produce clear errors.
28. Verify image type/size limits.
29. Verify image placement and scaling for `title_and_picture` and `title_content_and_picture`.

### Text and rendering

30. Verify long titles, subtitles, bullets, and headings do not silently disappear.
31. Verify text overflow behavior and whether slides auto-fit, clip, wrap, or error.
32. Verify bullet hierarchy is flat as documented and no unsupported nesting is assumed.
33. Verify Unicode, punctuation, and non-English text rendering.
34. Verify font availability/substitution behavior.

### Output and failures

35. Capture the exact tool response shape on success.
36. Capture the exact error response shape for malformed slides.
37. Verify generated `.pptx` persistence and accessible output reference/path.
38. Verify behavior for zero slides.
39. Verify behavior for very large presentations and any slide/count or payload limits.
40. Verify whether the output is deterministic for the same input and template.
