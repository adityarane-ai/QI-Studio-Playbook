# Compute Node

**Status:** Partially established conceptually. Exact expression/configuration semantics remain to be runtime-verified.

## Purpose

The Compute node derives or transforms state without requiring a direct user interaction. It is intended for deterministic transformations that can happen inside the orchestration.

## Contract currently established

- It reads values from available state.
- It derives or transforms one or more values.
- It writes the resulting value into state.
- It continues the orchestration without waiting for a human response.

A generic pattern is:

```text
existing state → Compute → derived state → downstream node
```

## Examples of suitable work

- arithmetic or scoring
- normalizing a value
- combining multiple state values
- deriving a classification or flag
- preparing a value for a later Condition or Tool Call

## Configuration areas to verify

- supported expression/function syntax
- exact reference syntax for state variables
- output/write-to-state configuration
- handling of null and missing inputs
- type coercion
- error behavior for invalid expressions
- whether a Compute step can write multiple keys
- ordering and loop-gating behavior when `Produces keys` is used

## Evidence policy

Do not promote assumptions about expression syntax or state mutation into confirmed product behavior until a controlled runtime test is recorded under `04-Evidence/`.