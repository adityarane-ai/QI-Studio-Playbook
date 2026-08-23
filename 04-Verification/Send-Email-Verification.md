# Verification Queue: Send Email

Status: Pending runtime verification
Source record: `14-Evidence/2026-08-23-Web-Search-and-Memory-Email-Attachment-Tools.md`

## Questions
- What provider/service actually sends the message?
- What sender addresses are permitted in `sender`?
- Is `sender` optional in practice, and what default sender is used?
- Are `contactCode`, `isRegisteredUser`, `userType`, and `legalCompanyName` required semantically or only schema-compatible fields?
- What validation is applied to email addresses and recipient objects?
- Can `tos`, `ccs`, and `bccs` contain null items despite their schemas?
- What are recipient count and email-size limits?
- How is invalid HTML handled?
- Is the `Regards, GEP Quantum` closing text enforced by runtime or only prompt guidance?
- Are scripts, external resources, and CSS blocks rejected, stripped, or merely discouraged?
- What attachment size/count/file-type limits apply?
- How are invalid or missing attachment IDs handled?
- Do export-tool file objects pass through unchanged?
- What delivery, retry, timeout, and failure statuses are returned?
- Can the tool send to external addresses, distribution lists, or only registered users?

## Promotion Rule
Move confirmed runtime findings into the evidence record and remove only the corresponding unresolved questions here.