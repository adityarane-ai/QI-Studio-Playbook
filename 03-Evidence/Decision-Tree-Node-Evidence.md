# Decision Tree Node Evidence Record

**Evidence ID:** QI-OBS-2026-08-23-DECISION-TREE-001  
**Date:** 2026-08-23  
**Capability:** Experimental internal flow builder, state-driven routing, Ask User, Tool Call, Compute, Condition, Done, memory/state handling.  
**Source:** User-supplied QI Studio Decision Tree screenshots and accompanying product guidance.

## Evidence inventory

The supplied screenshot set shows an internal Decision Tree canvas with a Start step and examples of internal steps. The Add first step menu visibly exposes:

- Talk to user
- Run a tool
- Compute
- Make a decision
- Finish

The screenshots also show configuration panels for Ask User, Tool Call, Compute, Condition, and Done.

## Direct observations

### Canvas

The Decision Tree canvas contains an internal **Start** entry point.

The guidance states that every step must ultimately connect back to Start to be executable.

The guidance also references:

- Fit / Auto-layout
- Memory Keys
- Issues / validation feedback

Example issues called out in the supplied guidance include:

```text
not connected to Start
no produces key — it may loop
```

### Ask User

The observed Ask User configuration exposes response modes:

- Types a reply
- Picks in a widget
- Handled to a person

The typed-reply mode exposes:

- Message to user
- Notes for the AI
- Information this step captures

The widget mode exposes:

- Runs only when
- Widget to show
- Notes for the AI
- Information this step captures

The human-handled mode exposes:

- Where the answer goes
- Parameters to pass
- Add a message
- Add notes for the AI
- Information this step captures

### Tool Call

The Tool Call configuration exposes:

- Runs only when
- Which tool to run
- Information sent to the tool
- Save the tool's results as
- Add tool
- Notes for the AI
- Information this step captures

The guidance uses `get_contract_by_number` as an example tool identifier.

The result-saving area allows the tool result to be remembered under a chosen name.

### Compute

The Compute configuration exposes:

- Runs only when
- Values this step adds to memory
- Values this step clears from memory
- Information this step captures
- Notes for the AI

This demonstrates explicit state mutation and state invalidation concepts inside the tree.

### Condition / Make a Decision

The Condition configuration exposes named paths with path-specific rules.

The observed structure contains:

- path name
- state field
- comparison operator
- value or `{{path}}`
- add another path
- first step of each path
- else path

The guidance states that paths are checked from top to bottom and the first matching path wins.

### Condition operators

The visible operator menu contains:

- Equals
- Not Equals
- Greater Than or Equal To
- Less Than or Equal To
- Greater Than
- Less Than
- Contains
- Is Empty
- Is Not Empty

### Done / Finish

The Done configuration exposes:

- Runs only when
- Message shown at the end

The supplied screenshot example is:

```text
All done, your request has been submitted.
```

## Core state concepts captured

The supplied product guidance documents:

- Produces keys
- State / Memory keys
- Extra conditions
- Message templates using `{{path}}`
- Notes for the AI
- Require reflection before this step fires

### Produces keys

A step's produces key marks the state key it fills and is used as a progress signal to reduce loop risk.

### State / Memory

Internal steps can read from and write to shared state.

### Invalidation

Compute steps can clear values from memory so stale data is not reused by later steps.

### Extra conditions

Steps may have extra execution gates in addition to the graph's inherent ordering.

### Reflection

The guidance recommends reflection gates sparingly, particularly before sensitive or irreversible actions.

## Product-guidance semantics captured

The supplied Decision Tree text describes it as a mini state-driven decision graph inside one node.

It explicitly defines the internal step families as:

- Ask user
- Tool call
- Compute
- Condition
- Done

It also explains that the internal graph carries state, uses templated paths, and can validate graph connectivity and loop-related issues.

## What this evidence proves

The screenshots and supplied product guidance establish that these UI controls and concepts exist in the observed QI Studio build/documentation.

They do not by themselves establish exact runtime semantics for all state mutation, loop prevention, tool retries, interruption/resume, or reflection behavior.

## What remains unverified

- Exact state object schema inside a Decision Tree execution.
- Exact produces-key completion semantics.
- Multiple produces keys in one step.
- Exact condition type coercion.
- Behavior with missing state fields.
- Tool timeout/retry behavior within a tree.
- Compute async behavior.
- State-clearing order relative to condition evaluation.
- Reflection payload and pause/resume mechanics.
- Maximum number of internal steps and paths.
- Tree interruption and resumption.
- Error recovery within a path.
- Exact human-handled Ask User integration semantics.

## Verification test matrix

| ID | Test | Purpose |
|---|---|---|
| DT-001 | Ask User -> Done | Validate basic internal lifecycle |
| DT-002 | Ask User -> Condition | Verify state capture and branching |
| DT-003 | Widget input | Verify picker value capture |
| DT-004 | Human-handled input | Verify delegated answer routing |
| DT-005 | Tool call -> save result | Verify state storage path |
| DT-006 | Compute add/clear | Verify state mutation semantics |
| DT-007 | Three paths with overlap | Confirm first-match ordering |
| DT-008 | Is Empty / Is Not Empty | Verify empty-state operators |
| DT-009 | Missing field condition | Determine missing-value behavior |
| DT-010 | Loop-prone step | Validate produces-key protection |
| DT-011 | Reflection gate | Verify pause/approval behavior |
| DT-012 | Tool failure | Determine error path behavior |
| DT-013 | Interrupted tree | Determine resume semantics |
| DT-014 | Large internal graph | Determine practical limits |

## Evidence handling rule

Do not store credentials, authorization values, runtime tokens, API keys, or sensitive business payloads in screenshots, examples, or committed evidence. Redact them before future evidence is added.
