# Script Node Evidence Record

**Evidence ID:** QI-OBS-2026-08-23-SCRIPT-001  
**Date:** 2026-08-23  
**Capability:** JavaScript execution, typed inputs, typed outputs, testing, error/output variables, state updates.  
**Source:** User-supplied QI Studio Script screenshots and accompanying product guidance.

## Observed UI

The supplied screenshot set shows a Script node with:

- Inputs section
- Edit params control
- Visual Editor / JSON Schema tabs
- Input fields with configurable types
- Code editor
- Test Script button
- Return (Output) section
- Visual Editor / JSON Schema tabs for output
- Add Field controls
- Required output-field toggle
- Advanced section
- Error Handling
- State Update
- Output Variables

## Input type evidence

The visible type picker contains:

- string
- number
- boolean
- object
- array<string>
- array<number>
- array<boolean>
- array<object>
- array<array>

The supplied configuration uses:

```text
score : number
Test value: 95
```

## Code evidence

The screenshot contains a JavaScript example that:

1. logs the input score
2. evaluates score thresholds
3. assigns a letter grade
4. logs the grade
5. returns `{ grade }`

The visible thresholds are:

```text
>= 85 -> A
>= 70 -> B
>= 50 -> C
else  -> F
```

## Output evidence

The visible output schema is:

```text
grade : string
Required: enabled
```

The code returns an object containing `grade`, matching the visible output contract.

## Runtime output variables observed

The Advanced section exposes:

| Variable | Type | Meaning |
|---|---|---|
| `result` | object | Script return object |
| `success` | boolean | Script execution success status |
| `error` | object | Error information when execution fails |
| `error.message` | string | Error message |
| `error.status_code` | number | Error status code |

## Product-guidance semantics captured

The supplied guidance states:

- inputs are available in code as `input.<field>`
- the script should return an object matching the output schema
- `nodeOutput.result` represents the current script result
- other nodes can read the result through `nodes.<nodeName>.result.<field>`
- Test Script is intended for execution against configured test values before wiring the node into the larger workflow
- Script is appropriate for custom calculation, transformation, reshaping, date formatting, and similar deterministic logic
- Rule is preferred for simple branching

## What screenshots do not prove

The screenshots do not establish exact runtime details for:

- JavaScript engine/version
- available globals/modules
- async/promise support
- execution timeout
- memory/resource limits
- external network access
- sandbox restrictions
- exact schema-validation enforcement
- error-handling retry semantics
- state-update atomicity

## Recommended follow-up tests

| ID | Test | Purpose |
|---|---|---|
| SCRIPT-001 | Run `score = 95` | Confirm example result is `A` |
| SCRIPT-002 | Boundary values 85/70/50 | Confirm comparison boundaries |
| SCRIPT-003 | Invalid score type | Confirm input/schema validation |
| SCRIPT-004 | Missing required input | Determine runtime failure behavior |
| SCRIPT-005 | Output type mismatch | Determine schema enforcement |
| SCRIPT-006 | Throw an Error | Capture error object behavior |
| SCRIPT-007 | Long-running script | Determine execution timeout |
| SCRIPT-008 | Async code | Determine Promise support |
| SCRIPT-009 | Large object input | Determine practical payload limits |
| SCRIPT-010 | Script state update | Confirm result vs persistent state behavior |
| SCRIPT-011 | Downstream consumption | Verify `nodes.<nodeName>.result.<field>` resolution |
| SCRIPT-012 | Logging | Determine where console output is surfaced |

## Security evidence rule

Do not log or persist Authorization values, RuntimeToken values, credentials, API keys, or unnecessary sensitive business payloads in Script code, tests, or evidence.
