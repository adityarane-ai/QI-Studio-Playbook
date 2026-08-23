# START API Runtime Notes

A concise companion to [API Integration](../04-Tools/API-Integration.md) focused on the START boundary.

## Invocation boundary

```text
Caller
  ↓
POST /invoke
  ↓
START
  ├─ interface.inputs
  └─ options
       ├─ sessionId
       └─ includeIntermediateParts
```

## Resume boundary

```text
Workflow pauses
  ↓
INPUT_REQUIRED
  ↓
Caller receives interrupt
  ↓
Human makes decision
  ↓
POST /invoke + options.resume
  ↓
Workflow continues from checkpoint
```

## Streaming boundary

The API Reference exposes Server-Sent Events for run, step, tool and text lifecycle events. See [API Integration](API-Integration.md) for the observed event taxonomy.
