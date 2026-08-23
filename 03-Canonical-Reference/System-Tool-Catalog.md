# System and Runtime Tool Catalog

This catalog captures the exact system-tool contracts supplied during the investigation.

## Web Search

**Tool:** `BraveWebSearch`

Inputs:

- `query` string
- `searchFromDate` string or null, required by schema

Returns a list of search results with `Name`, `Link`, and `Value`.

The supplied contract states that links used to produce a final answer must also be included in `citations`.

## Memory

### Set In Memory

**Tool:** `Store`

Inputs:

- `key`
- `value`

Stores a key-value pair in the current context memory bag. The contract explicitly restricts use to situations where there are clear and explicit instructions to store data in memory.

### Get From Memory

**Tool:** `Retrieve`

Input:

- `key`

Retrieves the value associated with the key from the current context memory bag. The contract likewise requires explicit instructions to retrieve memory.

## Email

**Tool:** `SendEmail`

Required inputs:

- `tos`
- `subject`
- `emailBody`

The email body must be valid HTML with inline styles and must close with `Regards, GEP Quantum` according to the supplied contract.

Optional inputs include CC, BCC, reply-to, sender, and attachments.

Attachments use objects containing `name` and `id`.

## Conversation Attachment

**Tool:** `ConversationAttachment`

Input:

- `fileId`

Returns uploaded session-file content as a string. The supplied contract states that the function should be called once per file when multiple attachments need to be read.

## Export File

**Tool:** `ExportBlob`

Input:

- `fileId`

Returns an object containing `Name` and `Id` for an existing blob file. The supplied contract states that the returned object must be included in the final response's `attachments` array.

## System Tool Discovery Protocol

### Search System Tools

**Tool:** `SearchSystemTools`

Input:

- `intent`

The supplied contract explicitly says this must be called first before executing any system action and that tool names must not be guessed.

### Get System Tool Schema

**Tool:** `GetSystemToolSchema`

Input:

- `toolNames` array

The supplied contract says this is called after discovery and before execution to obtain the exact argument schema.

### Execute System Tool

**Tool:** `ExecuteSystemTool`

Inputs:

- `tool_name`
- `arguments`

The supplied contract says the tool name must come from `SearchSystemTools` and the arguments must follow the schema returned by `GetSystemToolSchema`.

## Knowledge Workflow Initialization

**Tool:** `get-knowledge-workflow-instructions`

No inputs.

The supplied contract marks this as mandatory before any knowledge-related tool and says it must be called at the start of every agent invocation that involves knowledge sources.

## Evidence boundary

These are tool contracts supplied directly during the investigation. They establish intended configuration and ordering rules, but they do not by themselves prove every runtime behavior.
