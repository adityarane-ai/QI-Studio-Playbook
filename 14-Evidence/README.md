# Evidence Index

Evidence is treated as a first-class part of the playbook.

## Evidence hierarchy

```text
Screenshot / UI observation
        ↓
Evidence record
        ↓
Capability documentation
        ↓
Experiment
        ↓
Validated pattern
        ↓
Architecture guidance
```

## Evidence classes

- **CONFIRMED**: visible or directly reproducible in QI Studio.
- **TESTED**: implemented and observed at runtime.
- **DOCUMENTED**: explicitly stated by authoritative product documentation.
- **INFERRED**: architectural conclusion from evidence.
- **UNKNOWN**: not yet established.

## Current records

- [EVID-START-001: START Node and Runtime API](EVID-START-001.md)

## Screenshot policy

Raw screenshots should be retained where practical, but screenshots are not the knowledge layer by themselves. Every useful screenshot batch must receive a structured evidence record describing what it proves and what it does not prove.

## Change policy

When later screenshots contradict earlier evidence:

1. preserve the older record;
2. create a new evidence record;
3. explain the changed behavior;
4. update the canonical capability page;
5. update affected patterns and anti-patterns;
6. update the changelog.
