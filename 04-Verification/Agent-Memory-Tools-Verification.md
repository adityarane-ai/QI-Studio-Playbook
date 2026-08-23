# Agent Memory Tool Verification

**Date:** 2026-08-23  
**Scope:** `save_memory` and `update_memory` tools observed in the Agent tool configuration UI.

> These items are configuration-level observations only. They are not runtime-verified.

## Save Memory

### SM-001: Save result contract

**Current understanding:** `save_memory` accepts a single `content : string` representing one atomic fact to remember. The tool is configured to save a concise, durable fact for future conversations.

**Needs verification:** Exact return value/result object, whether a `memoryId` is returned, and whether metadata such as relevance, timestamp, or scope is exposed.

**Test:** Save a controlled fact and inspect the complete tool result and any downstream variables.

### SM-002: Duplicate detection and recall-before-save rule

**Current understanding:** The configured rules say to recall first to avoid saving duplicates.

**Needs verification:** Whether the platform itself rejects/deduplicates semantically equivalent memories or whether this is only an agent instruction.

**Test:** Save an exact duplicate, close paraphrase, and conflicting version of an existing memory and compare results.

### SM-003: Persistence timing

**Current understanding:** The tool is intended for durable facts used in future conversations.

**Needs verification:** Whether persistence is immediate, transactional, asynchronous, or deferred until workflow completion.

**Test:** Save a fact and immediately attempt recall within the same execution, then from a new session.

### SM-004: Content normalization and size limits

**Current understanding:** The tool accepts a string content field and instructs the agent to keep it concise and self-contained.

**Needs verification:** Maximum content length, whitespace handling, normalization, truncation, and rejection behavior for oversized content.

**Test:** Save controlled short, long, whitespace-heavy, and structured-string inputs.

## Update Memory

### UM-001: Update result contract

**Current understanding:** `update_memory` accepts `memoryId` and replacement `content`, replacing the stored fact while preserving scope.

**Needs verification:** Exact return value/result object and whether the updated memory identifier and metadata are returned.

**Test:** Update a controlled memory and inspect the complete tool result and downstream variables.

### UM-002: Invalid or inaccessible memory ID behavior

**Current understanding:** The configured guidance expects `memoryId` to come from a prior recall and not to be invented or modified.

**Needs verification:** Exact behavior for nonexistent, malformed, expired, or inaccessible memory IDs.

**Test:** Attempt updates using valid, nonexistent, malformed, and cross-scope IDs in a controlled environment.

### UM-003: Atomicity and replacement semantics

**Current understanding:** The description says the new content replaces the existing memory while preserving scope.

**Needs verification:** Whether replacement is atomic, whether the old value remains available during execution, and how concurrent updates behave.

**Test:** Perform controlled sequential and concurrent updates and inspect the resulting memory and recall behavior.

### UM-004: Scope preservation

**Current understanding:** The tool description explicitly says scope is preserved when the memory content is replaced.

**Needs verification:** Exact scope model and whether all scope metadata remains unchanged after an update.

**Test:** Update a memory with known scope metadata and compare before/after recall metadata.

## Promotion rule

When a runtime test confirms an item:

1. Record the observed result in the canonical Agent evidence record.
2. Update any affected Agent documentation.
3. Remove the verified item from this verification file.
4. Preserve the history through Git commits.

## Related

- `14-Evidence/2026-08-23-Agent-Node.md`
- `04-Verification/Verification-Queue.md`
- `02-Orchestration-Primitives/Agent.md`
