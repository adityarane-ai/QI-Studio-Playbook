# Governance

## Documentation contract

Every capability document must distinguish facts from recommendations.

### Evidence classes

- **CONFIRMED**: directly visible or reproducible in QI Studio.
- **TESTED**: implemented and observed.
- **DOCUMENTED**: stated in authoritative documentation.
- **INFERRED**: architectural interpretation.
- **UNKNOWN**: unresolved.

## Change protocol

When new evidence arrives:

1. Identify the capability affected.
2. Add the raw evidence reference.
3. Update the capability page.
4. Update examples and diagrams if behavior changed.
5. Record failures and workarounds.
6. Update interaction pages if another primitive is affected.
7. Update the changelog.
8. Preserve superseded information in historical notes.

## Screenshot protocol

Never store a screenshot without metadata.

Capture:

- evidence ID
- date
- product area
- capability
- what is visible
- what it proves
- what it does not prove
- related experiment
- related documentation

## Experiment protocol

Each experiment records:

- question
- hypothesis
- QI configuration
- inputs
- expected result
- actual result
- failure behavior
- workaround
- confidence
- evidence
- follow-up questions

## Naming

Use stable capability names rather than project-specific names. Project implementations should cross-link to platform pages.

## AI maintenance

Agents updating the repository must not silently rewrite platform facts. A correction should state the old belief, the new evidence, and why the interpretation changed.
