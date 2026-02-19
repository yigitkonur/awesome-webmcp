# ToolAnnotations — Metadata Hints for Tools

## Overview

The `ToolAnnotations` dictionary provides optional metadata about a tool's behavior.
Annotations help agents make informed decisions about when and how to invoke tools
without needing to understand the implementation details. The concept is aligned with
[MCP's annotation system](https://modelcontextprotocol.io/introduction).

## WebIDL Definition

```webidl
dictionary ToolAnnotations {
  boolean readOnlyHint;
};
```

## Usage

```javascript
navigator.modelContext.registerTool({
  name: "get-stamps",
  description: "Retrieve all stamps in the collection",
  annotations: {
    readOnlyHint: true
  },
  execute: async () => ({
    content: [{ type: "text", text: JSON.stringify(stamps) }]
  })
});
```

## readOnlyHint

| Property | Value |
|---|---|
| **Type** | `boolean` |
| **Required** | No |
| **Default** | `undefined` (not specified) |

From the [spec](https://webmachinelearning.github.io/webmcp):

> If true, indicates that the tool does not modify any state and only reads data.
> This hint can help agents make decisions about when it is safe to call the tool.

### Semantics

- **`readOnlyHint: true`** — Tool only reads data; does not modify state. Safe to
  call without user confirmation.
- **`readOnlyHint: false`** — Tool may modify state (create, update, delete operations).
  Agents should consider requesting user confirmation.
- **`readOnlyHint` omitted** — No hint provided. Agents should use the tool description
  to infer behavior.

### Important: These Are Hints, Not Guarantees

The word "hint" is deliberate. The browser cannot verify that a tool actually complies
with its annotation. A tool marked `readOnlyHint: true` could still modify state — the
annotation is a trust signal, not an enforcement mechanism.

> Annotations provide **deterministic hints** of the side-effects a tool execution can
> have. These are helpful for Agents to decide whether a tool should be used in a
> specific context.

## How Agents Use Annotations

### Permission Decisions

Agents can use annotations to determine what level of confirmation is needed:

```
readOnlyHint: true  → Agent may call without explicit user confirmation
readOnlyHint: false → Agent should request user confirmation before calling
readOnlyHint: omitted → Agent uses description + heuristics to decide
```

### Conversational Mode

From Issue #53:

> `readOnlyHint` could be used if the user wants an Agent to be in "conversational mode"
> only.

In conversational mode, an agent might:
1. Only call tools with `readOnlyHint: true`
2. Skip any tools that could modify state
3. Present state-modifying tools as suggestions rather than actions

### Example: Agent Decision Flow

```
User: "What stamps do I have?"

Agent sees:
  - get-stamps (readOnlyHint: true) → Safe to call directly
  - delete-stamp (readOnlyHint: false) → Would not call for this query
  - add-stamp (no annotation) → Would not call for this query

Agent calls get-stamps without confirmation.
```

## Practical Examples

### Read-Only Tools

```javascript
// Data retrieval — safe to call freely
navigator.modelContext.registerTool({
  name: "get-user-profile",
  description: "Get the current user's profile information",
  annotations: { readOnlyHint: true },
  execute: async () => ({
    content: [{ type: "text", text: JSON.stringify(userProfile) }]
  })
});

navigator.modelContext.registerTool({
  name: "search-products",
  description: "Search the product catalog",
  annotations: { readOnlyHint: true },
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search query" }
    },
    required: ["query"]
  },
  execute: async ({ query }) => ({
    content: [{ type: "text", text: JSON.stringify(await searchProducts(query)) }]
  })
});
```

### State-Modifying Tools

```javascript
// Creates data — should be annotated as non-read-only
navigator.modelContext.registerTool({
  name: "add-stamp",
  description: "Add a new stamp to the collection",
  annotations: { readOnlyHint: false },
  inputSchema: { /* ... */ },
  execute: async ({ name, year }) => {
    stamps.push({ name, year });
    renderStamps();
    return { content: [{ type: "text", text: `Added "${name}"` }] };
  }
});

// Deletes data — definitely not read-only
navigator.modelContext.registerTool({
  name: "delete-stamp",
  description: "Remove a stamp from the collection permanently",
  annotations: { readOnlyHint: false },
  inputSchema: {
    type: "object",
    properties: {
      name: { type: "string", description: "Name of the stamp to remove" }
    },
    required: ["name"]
  },
  execute: async ({ name }, client) => {
    // Request user confirmation for destructive action
    const confirmed = await client.requestUserInteraction(async () => {
      return confirm(`Delete stamp "${name}"? This cannot be undone.`);
    });
    if (!confirmed) throw new Error("Deletion cancelled");
    deleteStamp(name);
    return { content: [{ type: "text", text: `Stamp "${name}" deleted.` }] };
  }
});
```

## MCP Alignment

WebMCP's `ToolAnnotations` is aligned with [MCP's](https://modelcontextprotocol.io/introduction) annotation system. MCP defines
several annotation fields:

| MCP Annotation | WebMCP Equivalent | Status |
|---|---|---|
| `readOnlyHint` | `readOnlyHint` | ✅ Included in spec |
| `destructiveHint` | — | Future consideration |
| `idempotentHint` | — | Future consideration |
| `openWorldHint` | — | Future consideration |
| `title` | — | Future consideration |

### Issue #53 Discussion

From [Issue #53](https://github.com/webmachinelearning/webmcp/issues/53) (Khushal Sagar):

> MCP already has a concept of annotations to provide deterministic hints of the
> side-effects a tool execution can have.

The initial spec includes only `readOnlyHint` as the most fundamental annotation.
Additional annotations may be added as the spec matures.

## Future Annotation Types (Under Discussion)

### destructiveHint

Whether the tool performs irreversible actions:

```javascript
// Speculative — not yet in spec
annotations: {
  readOnlyHint: false,
  destructiveHint: true  // Action cannot be undone
}
```

### idempotentHint

Whether calling the tool multiple times with the same input produces the same result:

```javascript
// Speculative — not yet in spec
annotations: {
  readOnlyHint: false,
  idempotentHint: true  // Safe to retry
}
```

### openWorldHint

Whether the tool interacts with external systems beyond the page:

```javascript
// Speculative — not yet in spec
annotations: {
  readOnlyHint: false,
  openWorldHint: true  // Makes network requests, sends emails, etc.
}
```

## Relationship to Permission Model

Annotations interact with the broader permission model discussed in
[Issue #44 — Managing action-specific permissions](https://github.com/webmachinelearning/webmcp/issues/44):

> The browser manages a global: "can you be agentic on this site" permission. The site
> doesn't need to be involved for this permission. Action specific permissions — say an
> action is destructive (deletes some files). The site would likely want user consent
> before that action is taken.

Annotations provide the metadata that permission systems can use to make decisions:

```
Browser permission: "Allow agent on this site" → Global
Tool annotation: readOnlyHint: true → Agent can call without per-action consent
Tool annotation: readOnlyHint: false → Agent should request per-action consent
```

## Pattern: Categorizing Tools by Annotation

```javascript
const readOnlyTools = [
  { name: "get-stamps", description: "List all stamps", annotations: { readOnlyHint: true }, execute: getStamps },
  { name: "search-stamps", description: "Search stamps", annotations: { readOnlyHint: true }, execute: searchStamps },
  { name: "get-stamp-details", description: "Get stamp details", annotations: { readOnlyHint: true }, execute: getStampDetails },
];

const writeTools = [
  { name: "add-stamp", description: "Add a stamp", annotations: { readOnlyHint: false }, execute: addStamp },
  { name: "update-stamp", description: "Update a stamp", annotations: { readOnlyHint: false }, execute: updateStamp },
  { name: "delete-stamp", description: "Delete a stamp", annotations: { readOnlyHint: false }, execute: deleteStamp },
];

// Register based on user preference
navigator.modelContext.provideContext({
  tools: userPreferences.readOnlyMode
    ? readOnlyTools
    : [...readOnlyTools, ...writeTools]
});
```

## Related Issues

- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Related output schema discussion
- [Issue #53 — Semantic hints for side-effects](https://github.com/webmachinelearning/webmcp/issues/53) — Origin of annotations
- [Issue #45 — Tool categories](https://github.com/webmachinelearning/webmcp/issues/45) — Related discussion on categorization
- [Issue #44 — Action-specific permissions](https://github.com/webmachinelearning/webmcp/issues/44) — Permission model

## See Also

- [Tool registration interface](tool-registration-interface.md)
- [registerTool() method](register-tool.md)
- [Execute callback specification](execute-callback.md)
- [inputSchema for tool inputs](input-schema.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Security threat model](../04-security/threat-model-overview.md)
- [Tool poisoning attacks](../04-security/tool-poisoning.md)
- [Misrepresentation of intent](../04-security/misrepresentation.md)
- [Human-in-the-loop philosophy](../06-user-interaction/human-in-the-loop.md)
- [tooldescription attribute](../03-declarative-api/tooldescription-attribute.md)
