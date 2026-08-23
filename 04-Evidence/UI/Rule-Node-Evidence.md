# Rule Node Evidence Record

> **Capture date:** 2026-08-23
> **Source:** User-supplied QI Studio screenshots and accompanying product guidance.
> **Status:** Evidence captured. Runtime semantics remain partially unverified.

## Direct observations

The Rule UI shows:

- IF / CASE 1
- ELSE IF / CASE 2
- ELSE / DEFAULT
- Select field
- Operator selector
- Enter value
- `+ Add Condition`
- AND / OR grouping
- enable/disable toggles
- separate output handles
- `{{flow.fieldName}}` references

Visible operators: Equals, Not Equals, Greater Than or Equal To, Less Than or Equal To, Greater Than, Less Than, Contains, Is Empty, Is Not Empty.

The supplied guidance states that blocks are checked top-to-bottom and the first matching block wins; ELSE is the catch-all. Disabled blocks are skipped without being deleted, and removing a block removes its handle and connected edges.

## Evidence boundary

Screenshots do not establish complete runtime semantics for missing/null/empty values, case sensitivity, type coercion, date comparisons, collection behavior with Contains, nested Boolean grouping, or skipped-branch telemetry.

## Next tests

| ID | Test |
|---|---|
| RULE-001 | Exact text equality |
| RULE-002 | Text case variation |
| RULE-003 | Numeric threshold/coercion |
| RULE-004 | Empty vs missing |
| RULE-005 | Null handling |
| RULE-006 | Contains on text |
| RULE-007 | Contains on array |
| RULE-008 | Overlapping blocks / first-match |
| RULE-009 | AND |
| RULE-010 | OR |
| RULE-011 | Disabled matching block |
| RULE-012 | Block removal with edge |
| RULE-013 | Missing referenced variable |
| RULE-014 | Nested AND/OR grouping |

Do not store secrets or runtime credentials in evidence.
