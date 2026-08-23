# Verification Queue: Extract Document to Markdown

**Capability:** `extract_document_to_markdown`  
**Evidence date:** 2026-08-23  
**Status:** Configuration captured; runtime verification pending.

## Basic input/output

- [ ] Verify minimal valid PDF extraction.
- [ ] Verify required `fileId` and `fileName` validation.
- [ ] Verify omitted `bpc` and `sessionId` behavior.
- [ ] Verify returned JSON envelope contains `fileId`, `fileName`, and `filePath` with the documented meanings.
- [ ] Verify returned file name uses the lowercased original stem plus `.txt`.
- [ ] Capture exact success and error response shapes.

## Supported document formats

- [ ] Verify `.pdf`.
- [ ] Verify `.docx`.
- [ ] Verify `.pptx`.
- [ ] Verify legacy `.doc` conversion through LibreOffice.
- [ ] Verify legacy `.ppt` conversion through LibreOffice.
- [ ] Verify PNG, JPG, JPEG, GIF, WEBP, BMP, TIF, and TIFF image inputs.
- [ ] Test unsupported extensions and malformed files.

## PDF extraction pipeline

- [ ] Verify MarkItDown text extraction.
- [ ] Verify PyMuPDF image extraction with bounding boxes.
- [ ] Verify header/footer-band image filtering.
- [ ] Verify repeating watermark filtering.
- [ ] Verify overlapping-image composite stitching.
- [ ] Verify scanned-page detection.
- [ ] Verify 150 DPI rendering for scanned pages.
- [ ] Verify Tesseract OCR execution on image-dominant pages.
- [ ] Verify vision-LLM description of extracted images.
- [ ] Verify BlobProxy upload for image descriptions.
- [ ] Verify coordinate-based reading-order merge page → y → x.
- [ ] Verify final markdown upload to BlobProxy.

## Markdown image blocks

- [ ] Verify `<image_description>` wrapper.
- [ ] Verify Description field.
- [ ] Verify Steps list generation.
- [ ] Verify Brief Analysis generation.
- [ ] Verify image source URL or placeholder.
- [ ] Verify image descriptions appear in the correct location relative to nearby text.

## Storage and paths

- [ ] Verify `aibuildportal` container usage for document images.
- [ ] Verify `AI-DocImage/{bpc}/{session_id}/...` paths.
- [ ] Verify `Page{pageNum}_Image{groupNum}` naming.
- [ ] Verify `Page{pageNum}_Composite{groupNum}` naming.
- [ ] Verify `devazureblobtest` extracted-text container/path.
- [ ] Verify `AI-Export/{session_id}/{lowercased_original_stem}.txt` path.
- [ ] Verify file lifecycle, overwriting, and cleanup behavior.

## Vision LLM and authentication

- [ ] Verify `ds_leo.config.Tcs()` resolves the stated Azure OpenAI configuration.
- [ ] Verify actual vision model/provider used at runtime.
- [ ] Verify missing/invalid `PlatformAzureOpenAIBaseUrl` behavior.
- [ ] Verify missing/invalid `Platform2OpenAIApiKey` behavior.
- [ ] Verify BlobProxy bearer-token extraction from the incoming Authorization header.
- [ ] Verify missing/invalid bearer token behavior.
- [ ] Verify authorization is not incorrectly persisted or exposed in output.

## Quality and edge cases

- [ ] Verify reading order for multi-column PDFs.
- [ ] Verify tables, charts, diagrams, screenshots, and complex figures.
- [ ] Verify rotated pages and rotated images.
- [ ] Verify repeated headers and footers that should not become body content.
- [ ] Verify watermarks that overlap content.
- [ ] Verify pages with many images.
- [ ] Verify image-dominant but partially text-based pages.
- [ ] Verify OCR accuracy on representative scans.
- [ ] Verify extraction of non-English text and Unicode.
- [ ] Verify very large documents and practical file-size/page-count limits.
- [ ] Verify malformed PDFs and corrupted Office files.
- [ ] Verify deterministic extraction where practical.

## Evidence promotion rule

A verification item moves into confirmed evidence only after runtime observation, reproducible test results, or authoritative product documentation establishes the behavior. Remove confirmed items from this queue and record the test context and observed result in the evidence file.