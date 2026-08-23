# Evidence Record: Extract Document to Markdown

**Evidence ID:** QI-OBS-2026-08-23-EXTRACT-DOCUMENT-MARKDOWN-001  
**Date:** 2026-08-23  
**Capability:** Document and image content extraction to markdown with structured text extraction, image analysis, OCR for scanned PDFs, reading-order merging, and BlobProxy output.  
**Source:** User-supplied tool configuration JSON.

## Tool configuration

- Tool name: `extract_document_to_markdown`
- Tool title: `Extract Document to Markdown`
- Enabled: `true`
- Tool type: `mcp`
- Classification: external MCP, non-export system tool
- Required top-level inputs: `fileId`, `fileName`.
- Optional inputs: `bpc`, `sessionId`.
- The output is described as a JSON envelope containing `fileId`, `fileName`, and `filePath` for the extracted text file.

## Supported input formats

- PDF (`.pdf`)
- Word (`.docx`)
- PowerPoint (`.pptx`)
- Legacy Word (`.doc`), converted through LibreOffice then processed as `.docx`
- Legacy PowerPoint (`.ppt`), converted through LibreOffice then processed as `.pptx`
- Images: `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`, `.bmp`, `.tif`, `.tiff`

## What was observed

1. The tool extracts document text and image content and returns markdown-oriented output.
2. PDF processing begins with MarkItDown extraction of structured text from the whole document.
3. PyMuPDF is used to extract embedded PDF images and their on-page bounding boxes.
4. Header/footer-band images and repeating watermark images are filtered.
5. Overlapping images are stitched into composites before vision analysis.
6. Scanned, image-dominant pages without selectable text are rendered at 150 DPI and processed with Tesseract OCR.
7. Images are described by a vision LLM and uploaded to BlobProxy.
8. Text blocks and image descriptions are merged in reading order using page, y-coordinate, and x-coordinate ordering.
9. Final markdown content is uploaded to BlobProxy as plain text.
10. The output image blocks use an `<image_description>` wrapper containing Description, Steps, Brief Analysis, and an image-source reference/placeholder.
11. The image path pattern uses the `aibuildportal` container and an `AI-DocImage/{bpc}/{session_id}/...` path structure for page images and composites.
12. Extracted text is described as being stored in the `devazureblobtest` container under `AI-Export/{session_id}/{lowercased_original_stem}.txt`.
13. Vision LLM configuration is resolved through `ds_leo.config.Tcs()` for `PlatformAzureOpenAIBaseUrl` and `Platform2OpenAIApiKey`.
14. BlobProxy authorization uses the incoming HTTP Authorization bearer token from the tool request header.
15. The configuration states that the BlobProxy image source uses a path-style BlobProxy URL.
16. `fileName` is used for format detection, while the returned extracted file name is the lowercased original stem with `.txt`.
17. The supplied contract names the pipeline components and storage paths, but those statements remain configuration evidence until runtime-tested.

## What this evidence proves

The supplied configuration establishes the supported input formats, declared extraction pipeline stages, declared OCR and vision behavior, markdown image-block structure, declared storage/path patterns, required parameters, and stated authentication/configuration dependencies.

## What this evidence does not prove

The supplied configuration does not independently prove OCR accuracy, vision-description quality, reading-order correctness for complex layouts, image filtering precision, composite stitching quality, support for every listed file subtype in practice, LibreOffice conversion fidelity, BlobProxy URL accessibility, authentication behavior under missing/invalid bearer tokens, storage permissions, cleanup/lifecycle behavior, malformed-file handling, maximum file sizes, timeouts, exact returned JSON on success/failure, deterministic extraction, or prompt/model configuration used by the vision LLM.

## Verification

Runtime verification is pending and belongs in `04-Verification/Extract-Document-to-Markdown-Verification.md`.