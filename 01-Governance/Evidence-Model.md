# Evidence Model

## Purpose

The playbook distinguishes what is visible, what is documented, what has been reproduced at runtime, and what remains unresolved.

## Active evidence states

| State | Meaning |
|---|---|
| **Observed** | Visible in the QI Studio UI, screenshots, runtime envelope, or other supplied artifact. |
| **Documented** | Explicitly stated in authoritative product guidance supplied to the investigation. |
| **Runtime Confirmed** | Reproduced through a controlled runtime test with a recorded result. |
| **Open** | Identified but not sufficiently established by evidence or runtime testing. |

States may be combined when useful, for example `Observed + Documented`.

## Promotion rule

A claim becomes **Runtime Confirmed** only when a controlled test reproduces it and the result is recorded in `04-Evidence/`.

A documented feature is not automatically runtime confirmed.

## Contradictions

Contradictions are events, not permanent evidence states.

When a test disproves a previous assumption:

1. record the failed or contradictory observation in evidence/history;
2. update the canonical reference;
3. remove the resolved question from verification;
4. preserve the historical record through Git history and evidence notes.

Do not create a second permanent truth for a superseded claim.

## Security

Never place credentials, access tokens, authorization headers, API keys, or other secrets in evidence, tests, examples, diagrams, or repository files.
