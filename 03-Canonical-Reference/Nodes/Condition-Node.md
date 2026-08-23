# Condition Node

**Status:** Partially established from product concepts and orchestration behavior. Exact configuration and evaluation semantics remain to be runtime-verified.

## Purpose

The Condition node evaluates state and selects one of multiple downstream paths based on whether a condition is satisfied.

## Contract currently established

- It acts as a branching point in an orchestration.
- Its decision should be based on values already available in state.
- Different outgoing paths represent different outcomes.
- Conditions should be tested with controlled values rather than inferred from a single successful execution.

## Relationship to other nodes

A common pattern is:

```text
Start → collect/derive state → Condition → path A / path B
```

A Condition can follow a Human Input, Tool Call, Compute step, or another state-producing node.

## Configuration areas to verify

- exact condition expression syntax
- supported operators
- handling of missing or null variables
- string versus numeric comparison behavior
- boolean coercion behavior
- whether conditions can reference nested objects
- behavior when multiple branches could match
- behavior when no branch matches
- whether evaluation is deterministic across resumed runs

## Evidence policy

This document defines the conceptual contract only. Exact UI fields, expression syntax, routing behavior, and edge cases must be promoted from `04-Evidence/` after controlled tests.
