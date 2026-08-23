# Verification Queue: Conversation Attachment

Status: Pending runtime verification
Source record: `14-Evidence/2026-08-23-Web-Search-and-Memory-Email-Attachment-Tools.md`

## Questions
- What file types and encodings are actually supported at runtime?
- Does the returned value always contain the complete file content as a string?
- What happens for binary files, unsupported formats, large files, empty files, or corrupt files?
- What are file-size and content-length limits?
- Does `fileId` refer only to files uploaded in the current conversation/session?
- What authorization and isolation checks apply to a file ID?
- What happens for an invalid, expired, deleted, or inaccessible file ID?
- When multiple files are present, does each call return content independently without cross-file contamination?
- Are `[image source path]` references actually preserved exactly, and what formats can those paths take?
- Does the runtime enforce image-first behavior, or is it model guidance only?
- Does the tool expose metadata such as filename, MIME type, or size, or only content?

## Promotion Rule
Promote confirmed runtime behavior into the evidence record and remove only the resolved items from this queue.