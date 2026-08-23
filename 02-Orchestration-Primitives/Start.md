# Start Node

> **Evidence status:** OBSERVED + DOCUMENTED
> **Evidence date:** 2026-08-23
> **Primary evidence:** User-supplied QI Studio orchestration screenshots and product guidance from the current project conversation.

The **Start** node is the entry point of a QI Studio orchestration. It establishes where the workflow begins and exposes the initial workflow inputs visible in the supplied orchestration canvas.

## Observed configuration

The supplied canvas shows a Start step with an **Entry point** indicator. The visible input surface includes values such as:

- `message`
- `attachments`
- `ui_action`

The exact full input contract should be confirmed through the runtime variable browser and node schema in the current QI Studio build.

## Conceptual role

```mermaid
flowchart LR
    I[External / user invocation] --> S[Start]
    S --> N[First orchestration step]
```

Start should be treated as an intake boundary, not as a place to encode business logic.

## Design guidance

Use Start to establish the initial context passed into the workflow. Validate and normalize inputs in explicit downstream steps.

Prefer:

```text
Start
  ↓
Input validation / normalization
  ↓
Decision / Agent / Tool / Subflow
```

over putting extensive transformation logic directly into the invocation contract.

## Important distinctions

- **Start inputs** are invocation data.
- **Variables** are workflow state.
- **Artifacts** are structured, versionable outputs.

Do not assume that every Start input should become persistent state.

## Open questions

- Exact schema and requiredness of every built-in Start input.
- Exact representation of attachments and UI actions.
- Whether custom Start inputs are supported and how they are typed.
- Input validation behavior before the first downstream node executes.
- Whether multiple entry-point invocation modes produce different initial metadata.

## AI-agent interpretation rules

1. Treat Start as the orchestration entry boundary.
2. Read only the inputs actually exposed by the current workflow invocation.
3. Do not invent built-in input names or types.
4. Move normalization, validation, and business logic into explicit downstream primitives.
5. Preserve uncertainty around undocumented invocation metadata until runtime-tested.
