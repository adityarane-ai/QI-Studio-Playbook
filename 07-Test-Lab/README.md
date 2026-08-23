# 07 Test Lab

This layer designs tests before they are executed.

## Separation rule

```text
07 Test Lab
    ↓ test design
QI Studio runtime
    ↓ actual execution
04 Evidence
    ↓ observed result
03 Canonical Reference
    ↓ updated truth
05 Verification
    ↓ remaining questions
```

## Planned contents

- `Test-Strategy.md` - principles for efficient end-to-end testing
- `Node-Test-Suites/` - small, high-information test suites for nodes
- `Tool-Test-Suites/` - high-information tests for tools
- `Fixtures/` - safe, reusable test inputs that contain no secrets

Tests should minimize count while maximizing information gained. Prefer a small number of carefully designed tests that discriminate between competing hypotheses.
