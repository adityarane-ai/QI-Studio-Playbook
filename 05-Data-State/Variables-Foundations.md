# Variables: Foundations and Contract Design

> Evidence basis: QI Studio variable editor screenshots supplied 2026-08-23.

## What a variable represents

A QI variable is a named value with a declared type and configuration contract. The observed editor supports scalar and object values and exposes validation/default controls.

## Scalar contract

The visible editor supports:

```text
name
 type
 description
 required
 validation:
   min length
   max length
   regex
   allowed values / enum
 default
```

Example:

```yaml
name: supplier_id
type: string
required: true
pattern: '^SUP-[0-9]{4}$'
```

## Object contract

The visible object editor supports a visual field editor or JSON Schema, plus Additional Properties.

Recommended practice:

```text
Object input
   |
   +-- define fields explicitly
   +-- define types
   +-- define required fields
   +-- decide whether additional properties are allowed
   +-- define defaults only where semantically safe
```

Avoid using a generic `{key: value}` object as a substitute for a real contract when downstream nodes depend on stable structure.

## Variable ownership

Classify every variable as one of:

| Ownership | Example | Who should write |
|---|---|---|
| System-owned | session/run identity | Runtime/workflow |
| Workflow-owned | evaluation configuration | Workflow + approved human gate |
| Agent-produced | semantic recommendation | Agent |
| Human-provided | clarification | Human Input |
| External-source | uploaded file metadata | Connector/runtime |

The agent should not silently rewrite a workflow-owned variable. Return a proposed value and let a Variable Update or later deterministic gate commit it.

## State operations

Pair variable contracts with explicit mutation semantics:

```text
Set    = replace
Append = add to collection/history
Clear  = remove stale value
```

For mutable state, document whether the value is allowed to be rewritten and whether a previous value must be preserved for audit/provenance.

## Validation strategy

Use deterministic validation for:

- required fields
- string length
- regex/pattern
- enum/allowed values
- basic type shape

Use an Agent only for semantic validation such as whether a description is substantively compliant with a requirement.

## Unknowns to test

- Runtime error shape for invalid scalar values.
- Runtime error shape for invalid object payloads.
- Null vs missing behavior.
- Default-value precedence when an input is omitted.
- Whether object Additional Properties is enforced at runtime.
- Validation timing relative to START state updates.
