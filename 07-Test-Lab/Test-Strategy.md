# Test Strategy

## Goal

Learn the most about QI Studio with the fewest reliable tests.

## Principles

1. Test one uncertainty at a time where possible.
2. Prefer paired tests that differ by one configuration variable.
3. Test the boundary between nodes, not only isolated node success.
4. Capture the exact downstream value, not just a generic success flag.
5. Use failure tests deliberately when they distinguish competing explanations.
6. Keep test inputs small and reproducible.
7. Preserve the workflow configuration used for a meaningful result.

## Test lifecycle

```text
Hypothesis
   ↓
Minimal test design
   ↓
Run in QI Studio
   ↓
Capture runtime result
   ↓
04-Evidence/Runtime
   ↓
Update canonical truth
   ↓
Remove resolved question from 05-Verification
```

## Test result standard

Every runtime test should state:

- what was being tested;
- exact configuration difference from the baseline;
- input;
- expected behavior;
- observed behavior;
- downstream evidence;
- conclusion;
- remaining uncertainty.
