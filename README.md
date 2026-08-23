# QI Studio Architecture Playbook

A reusable, evidence-driven knowledge base for designing, implementing, testing, and maintaining QI Studio orchestrations.

## Purpose

This repository is intended to let a human or AI agent understand QI Studio without reconstructing platform behavior from prior conversations.

It captures:
- what each QI Studio primitive is
- how it is configured
- how it behaves at design and runtime
- when to use it
- when not to use it
- how primitives interact
- working examples
- failed approaches and workarounds
- known limitations and unknowns
- tested patterns
- diagrams and decision guides
- version/change history

## Evidence discipline

Every important claim is classified as one of:

| Status | Meaning |
|---|---|
| CONFIRMED | Directly observed in QI Studio UI or runtime with strong confidence |
| TESTED | Implemented and observed in an experiment |
| DOCUMENTED | Explicitly stated by authoritative platform material |
| INFERRED | Strong architectural inference, not directly verified |
| UNKNOWN | Not established yet |

Never silently upgrade an INFERRED or UNKNOWN behavior to CONFIRMED.

## Core architectural principle

> Workflow controls the business process. Agents solve bounded reasoning problems. Tools retrieve or execute. Artifacts carry state and evidence. Human approvals create authority.

## Navigation

- [AI Index](AI_INDEX.md)
- [Architecture](ARCHITECTURE.md)
- [Governance](00-Governance/README.md)
- [Platform Overview](01-Platform-Overview/README.md)
- [Orchestration Primitives](02-Orchestration-Primitives/README.md)
- [Agent Architecture](03-Agent-Architecture/README.md)
- [Tools](04-Tools/README.md)
- [Data and State](05-Data-State/README.md)
- [Knowledge](06-Knowledge/README.md)
- [Document Processing](07-Document-Processing/README.md)
- [Human in the Loop](08-Human-in-the-Loop/README.md)
- [Design Patterns](09-Design-Patterns/README.md)
- [Anti-Patterns](10-Anti-Patterns/README.md)
- [Testing](11-Testing/README.md)
- [Reference Implementations](12-Reference-Implementations/README.md)
- [Bid Analysis](13-Bid-Analysis/README.md)
- [Evidence](14-Evidence/README.md)

## How to use this repository as an AI agent

Before designing an orchestration:

1. Read `AI_INDEX.md`.
2. Read the relevant primitive pages.
3. Check design patterns and anti-patterns.
4. Check evidence and experiments for actual observed behavior.
5. Explicitly separate platform facts from architectural recommendations.
6. Never invent an undocumented capability.
7. When a behavior matters and is marked UNKNOWN, design a test before relying on it.
8. Preserve links and backlinks when adding knowledge.

## Maintenance rule

When QI Studio changes, update the affected capability page, evidence status, examples, diagrams, interaction pages, and changelog together. Keep superseded knowledge in `99-Archive/` rather than deleting historical evidence.
