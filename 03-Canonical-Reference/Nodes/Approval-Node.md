# Approval Node

**Status:** Partially established from product guidance. Runtime behavior beyond basic routing remains unverified.

## Purpose

The Approval node introduces a human checkpoint into an orchestration. It pauses execution and waits for a person to choose between two outcomes, normally **Approve** and **Reject**.

## Contract currently established

- The node presents an approval message to the person.
- The node presents two decision actions.
- The labels can be customized to fit the business action, for example `Send it` / `Hold` or `Publish` / `Cancel`.
- The node has two logical outgoing routes:
  - **Approved**: continue the workflow.
  - **Rejected**: take an alternate path or terminate.
- Output variables can be used to preserve the decision for later steps.

## Intended use

Use Approval where a human sign-off is required before a sensitive, consequential, or irreversible action.

Examples include:

- sending an external communication
- publishing content
- changing or deleting records
- releasing a financial or operational action

## Configuration areas to verify

The following details are not considered runtime-confirmed yet:

- exact output-variable shape
- whether the decision is also exposed through a standard system variable
- behavior when the person rejects
- behavior when a response is missing or delayed
- whether approval can be resumed across sessions
- interaction with conditions and downstream state gating

## Evidence policy

Do not infer runtime behavior from the visual description alone. Promote claims into the confirmed reference only after controlled execution evidence is recorded under `04-Evidence/`.
