# Verification Queue: Memory Store / Retrieve

Status: Pending runtime verification
Source record: `14-Evidence/2026-08-23-Web-Search-and-Memory-Email-Attachment-Tools.md`

## Questions
- What is the exact lifecycle of the memory bag?
- Is memory scoped to a conversation, user, agent run, workflow run, session, or another context?
- Does `Store` overwrite an existing key or reject/merge it?
- Are nested objects, arrays, nulls, and large values supported?
- What serialization or type coercion occurs?
- What happens when `Retrieve` requests a missing key?
- Are keys case-sensitive?
- Are keys namespaced between agents, tools, or contexts?
- Does memory survive orchestration pauses, resumes, retries, or new runs?
- Are there size, count, TTL, or security constraints?
- Is explicit user instruction required at runtime, or is this constraint enforced by policy/model behavior only?

## Promotion Rule
Promote tested findings into the evidence record and delete only the resolved questions from this queue.