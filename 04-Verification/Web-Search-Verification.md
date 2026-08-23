# Verification Queue: Web Search

Status: Pending runtime verification
Source record: `14-Evidence/2026-08-23-Web-Search-and-Memory-Email-Attachment-Tools.md`

## Questions
- What search engine/provider and API version are used at runtime?
- What is the maximum result count and is pagination supported?
- How does `searchFromDate` behave with null, timestamps, timezone offsets, and future dates?
- Does the filter apply to discovery date, publication date, crawl date, or another timestamp?
- What exact runtime JSON shape is returned for results?
- Are snippets truncated, normalized, or HTML-sanitized?
- What happens on zero results, malformed queries, rate limits, timeouts, and provider failures?
- Is citation inclusion validated/enforced by the tool/runtime or merely guidance to the model?
- What happens when a used result lacks a usable link?

## Promotion Rule
Move verified findings into the evidence record when tested. Remove only the corresponding unresolved question from this verification file once the behavior is confirmed.