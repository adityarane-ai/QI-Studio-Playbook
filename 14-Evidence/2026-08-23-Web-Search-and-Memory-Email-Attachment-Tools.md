# Evidence Record: Web Search, Memory, Email, and Conversation Attachment Tools

Date captured: 2026-08-23
Source: user-supplied tool configuration JSON
Status: Configuration evidence only. Runtime behavior remains subject to verification.

## 1. BraveWebSearch / Web Search

Tool name: `BraveWebSearch`
Title: `Web Search`
Type: system tool via MCP/current module.
Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-BraveWebSearch-16`

Inputs:
- `query`: string, described as keywords to search for.
- `searchFromDate`: required field, nullable string with `date-time` format. Description says results are filtered by when they were discovered, from the supplied date through today.

Declared output semantics:
- Returns a list of web search results.
- Each result contains `Name`, `Link`, and `Value`.
- `Name`: text search result name.
- `Link`: associated link reference.
- `Value`: snippet describing the web page.

Declared citation requirement:
- When results from this function are used in a final answer, sources must also be included in `citations`, with the result `Link` sent on `citation.path`.

## 2. Store / Set In Memory

Tool name: `Store`
Title: `Set In Memory`
Type: system tool via MCP/current module.
Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-Store-17`

Purpose:
- Stores a key-value pair in the memory bag associated with the current context.

Inputs:
- `key`: string, unique identifier for the stored data.
- `value`: unrestricted schema in supplied configuration.

Declared output:
- Success message if data is stored successfully.

Important declared constraint:
- The tool should be used only when there are clear and explicit instructions to store data in memory.

## 3. Retrieve / Get From Memory

Tool name: `Retrieve`
Title: `Get From Memory`
Type: system tool via MCP/current module.
Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-Retrieve-18`

Purpose:
- Retrieves a value from the memory bag using a specified key.

Inputs:
- `key`: string, unique identifier of the data to retrieve.

Declared output:
- The value associated with the key if found.

Important declared constraint:
- The tool should be used only when there are clear and explicit instructions to retrieve data from memory.

## 4. SendEmail / Send Email

Tool name: `SendEmail`
Title: `Send Email`
Type: system tool via MCP/current module.
Tool ID: `894de768-1ac3-48a9-8210-89f6c2e33ae6-SendEmail-19` is the user-provided record identifier; configuration tool ID is `894de768-1ac3-48a9-9fd0-ba61fa37dbee-SendEmail-19`.

Purpose:
- Sends an email to one or more recipients.
- Supports To, CC, BCC, reply-to, and optional file attachments.
- Attachments are supplied as objects containing `name` and `id`, identifying a file in blob storage. The description explicitly says this can include objects returned by export tools.

Required inputs:
- `tos`: array of recipient objects; each recipient requires `email`.
- `subject`: string.
- `emailBody`: string and must be fully generated HTML.

Recipient object fields:
- `firstName`: nullable string.
- `lastName`: nullable string.
- `email`: nullable string but required property.
- `contactCode`: integer.
- `isRegisteredUser`: boolean.
- `userType`: nullable string.
- `legalCompanyName`: nullable string.

Optional recipient groups:
- `ccs`: nullable array of recipient objects.
- `bccs`: nullable array of recipient objects.

Optional sender controls:
- `replyTo`: nullable recipient-like object with required `email` property.
- `sender`: nullable recipient-like object with required `email` property.

Optional attachments:
- `attachments`: nullable array.
- Each attachment object supports `name` and `id`, both nullable strings.

Email body requirements declared by the tool:
- Must be valid HTML, not plain text.
- Standard HTML tags such as paragraphs, headings, lists, tables, and inline emphasis may be used.
- Formatting must use inline styles.
- No `<style>` blocks or external CSS.
- No `<script>` tags or external resources.
- Must close the email with `Regards, GEP Quantum`.

## 5. ConversationAttachment / Conversation Attachment

Tool name: `ConversationAttachment`
Title: `Conversation Attachment`
Type: system tool via MCP/current module.
Tool ID: `894de768-1ac3-48a9-9fd0-ba61fa37dbee-ConversationAttachment-20`

Purpose:
- Exports the content of a user-uploaded session file based on a provided file ID.
- Returns the file content as a string.

Input:
- `fileId`: string, required, identifying the uploaded file whose content should be read.

Declared usage guidance:
- Call this tool multiple times when multiple uploaded files must be read, using the corresponding file IDs.

Declared image-handling guidance when returned content contains `[image source path]` references:
1. Prioritize images over text when explaining steps or concepts.
2. Preserve each `[image source path]` exactly as returned.
3. Introduce the image briefly, insert the image-source tag, then continue with transitional text.
4. Include relevant images by default in responses.

## Verification Boundary

The supplied configuration establishes interface shape and declared behavior only. The following remain runtime questions unless independently tested:
- Actual search provider behavior, ranking, freshness, pagination/limits, and error handling.
- Exact semantics of `searchFromDate`, including timezone and discovery-date interpretation.
- Exact shape and serialization of search result objects at runtime.
- Citation enforcement and failure behavior when citations are omitted.
- Persistence scope, lifecycle, namespace, overwrite behavior, and serialization rules of the memory bag.
- Whether `Store` and `Retrieve` share the same context/session scope across nodes, runs, or conversations.
- Missing-key behavior for `Retrieve`.
- Email delivery provider, sender restrictions, authentication behavior, size limits, HTML sanitization, and failure/retry semantics.
- Whether attachments from export nodes are always accepted and how missing/invalid file IDs behave.
- Actual file formats/content handling returned by `ConversationAttachment`.
- Whether image-source directives are enforced by the runtime or are solely prompt guidance.
