# Tool and Integration Model

> **Status:** Current platform model based on supplied tool definitions, observed configuration surfaces, and selected runtime evidence. Tool contract presence is separated from successful runtime execution.

## 1. Why Tools Need Their Own Model

Tools are the action boundary between workflow/Agent reasoning and concrete capabilities.

A tool should be thought of as:

```text
Named capability
    +
typed input contract
    +
execution boundary
    +
result/artifact contract
```

The Agent decides when a tool is needed. The tool performs the defined action.

## 2. Tool vs Node

A node is a graph execution primitive.

A tool is a capability that can be invoked by an Agent or provided through a Skill/integration surface.

Conceptually:

```text
Workflow graph
    |
    +--> Node execution
    |
    +--> Agent
            |
            +--> Tool
```

Do not treat every tool as a replacement for a native workflow node. Native nodes are preferable when explicit graph/state semantics are required.

## 3. Tool Contract

A useful tool contract consists of:

- tool name
- description
- required parameters
- optional parameters
- parameter types
- agent-controlled vs fixed parameters
- result/output shape
- artifact behavior
- error behavior

The UI/tool definition establishes the declared contract. Runtime testing is required to establish actual execution behavior.

## 4. Tool Capability Families

Current evidence covers the following broad families:

### Export and artifact tools

- Excel V2
- PowerPoint V2
- PDF V2
- Word V2
- HTML V2

### Document and file tools

- document extraction
- conversation attachment handling
- blob/file retrieval

### Search and web tools

- web search
- system-action discovery/execution

### Memory/information tools

- Store
- Retrieve
- knowledge workflow/instruction tooling

### External action tools

- email
- connected/external integrations

## 5. Agent Tool Governance

The current platform guidance establishes a system-action discovery pattern for system tools:

```text
Need a system action
        ↓
SearchSystemTools
        ↓
GetSystemToolSchema
        ↓
ExecuteSystemTool
        ↓
Validate output
```

The important principle is: do not guess a system-tool name when the platform provides discovery.

## 6. Skills and Tools

A Skill can package specialized operating instructions and tools.

Conceptually:

```text
Agent
  |
  +-- Skill
        |
        +-- instructions
        +-- tool(s)
```

This is valuable when a capability has its own operating methodology.

For example, report generation can be owned by a Skill while Excel export remains the concrete tool.

## 7. Export Excel V2

The declared Excel V2 contract requires:

```text
filename
sheets
```

Each sheet requires:

```text
name
data
```

Supported cell values include:

- string
- number
- boolean
- ISO date string
- Excel formula beginning with `=`
- null

Optional sheet capabilities include:

- headers
- freeze panes
- autofilter
- tab color
- column widths
- header formatting
- column-scoped cell formatting
- conditional formatting
- charts

Supported chart types include bar, column, line and pie.

Percentage values should be supplied as fractions when using percentage number formats.

## 8. Artifact Boundary

Export tools create artifacts, but the exact lifecycle from tool result to persisted file to user-visible attachment must be runtime tested.

Do not assume that a successful tool call automatically means:

```text
tool success
    =
artifact persisted
    =
artifact available to Output
```

Those are separate stages.

The current verification queue therefore treats export execution and artifact persistence as explicit runtime tests.

## 9. File and Attachment Model

The platform exposes file-related capabilities such as:

- `system.files`
- attachments
- ConversationAttachment
- ExportBlob

The conceptual flow is:

```text
Input file / attachment
        ↓
Workflow or Agent
        ↓
Document/file tool
        ↓
Structured content or artifact
        ↓
Downstream workflow / Output
```

The exact representation and persistence semantics must be validated for each tool.

## 10. Knowledge Tool Boundary

Knowledge-related tooling should be treated as a distinct capability boundary.

The supplied platform guidance requires the knowledge workflow instructions to be obtained before using knowledge-related tools in relevant Agent invocations.

This establishes a prerequisite pattern:

```text
Knowledge task
    ↓
Knowledge workflow instructions
    ↓
Knowledge tool use
    ↓
Validate retrieved context
    ↓
Agent reasoning
```

The exact runtime enforcement of this prerequisite remains a verification item.

## 11. Search Tool Boundary

Search is an external information capability.

Search output should not automatically become authoritative business state.

A robust pattern is:

```text
Search
  ↓
source result
  ↓
validate relevance / provenance
  ↓
Agent interpretation
  ↓
explicit state if needed
```

## 12. Memory Tools

Store/Retrieve are distinct from Flow Variables and Long-term Memory controls exposed by Agents.

Do not assume these mechanisms share the same persistence scope.

The current Playbook treats memory persistence scope as an open verification item.

## 13. External Action Tools

Email and other external actions should be treated as consequential capabilities.

A tool that sends something externally should have:

- explicit parameters
- deliberate invocation
- validation of recipient/content
- appropriate human authorization where required
- observable result handling

Do not hide consequential actions inside arbitrary semantic generation.

## 14. Tool Result Handling

A tool result may be used in several ways:

```text
Tool result
    |
    +--> Agent context
    +--> Node/result output
    +--> Flow state via State Update
    +--> Artifact/file lifecycle
```

The correct destination depends on whether the result is transient context, business state, or a generated artifact.

## 15. Tool Parameters: Dynamic vs Fixed

Agent-exposed tool parameters can be treated as:

### Dynamic

The Agent decides the value at invocation time.

Use for:

- task
- query
- relevant data
- filename when generated dynamically
- sheets/report data

### Fixed

The workflow designer defines the contract.

Use for:

- expected output contract
- protected configuration
- static policy instructions
- tool constraints

A strong pattern is to keep structural constraints fixed while allowing task-specific data to remain dynamic.

## 16. Tool Errors

Tool errors are distinct from Agent reasoning failures.

A robust orchestration should preserve:

```text
execution status
error object
error message
artifact status where relevant
```

Do not turn a failed tool call into a successful workflow result merely because the Agent can describe what it intended to do.

## 17. Tool Selection Principle

Use the smallest capability that correctly accomplishes the action.

```text
Need exact state change -> native state node
Need exact calculation -> Script / Compute
Need semantic judgement -> Agent
Need concrete external action -> Tool
Need reusable specialized operating method -> Skill
Need external system access -> Connector
```

## 18. Design Principle: Contract Before Invocation

Before a tool is invoked in a production workflow, define:

1. what it does
2. what it needs
3. what it returns
4. where its result goes
5. what happens on failure
6. whether the action is reversible or consequential

This makes tools composable rather than opaque.

## 19. Evidence Boundary

The `04-Evidence/Tool-Contracts/Agent-Tool-Catalog.md` file records supplied tool contracts. Those contracts establish declared capability, not successful runtime execution.

The verification queue tracks runtime questions including:

- export execution
- artifact persistence
- attachment delivery
- document extraction fidelity
- web/search result propagation
- memory persistence
- system-tool discovery and execution
- knowledge workflow enforcement

Those questions should be resolved through controlled tests before being promoted to stronger runtime claims.
