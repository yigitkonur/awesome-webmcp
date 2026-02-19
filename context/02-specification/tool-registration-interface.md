# ModelContextTool Dictionary — Tool Registration Interface

## Overview

The `ModelContextTool` dictionary defines the shape of a tool that can be registered
with the browser via `registerTool()` or `provideContext()`. It contains the metadata
agents need to understand the tool, plus the callback function that implements it.
The formal definition is in the [spec](https://webmachinelearning.github.io/webmcp)
([PR #75 — WebIDL](https://github.com/webmachinelearning/webmcp/pull/75)).

## WebIDL Definition

```webidl
dictionary ModelContextTool {
  required DOMString name;
  required DOMString description;
  object inputSchema;
  required ToolExecuteCallback execute;
  ToolAnnotations annotations;
};
```

## Fields

### name (required)

| Property | Value |
|---|---|
| **Type** | `DOMString` |
| **Required** | Yes |
| **Purpose** | Unique identifier agents use to reference the tool |

From the [spec](https://webmachinelearning.github.io/webmcp):

> A unique identifier for the tool. This is used by agents to reference the tool
> when making tool calls.

**Naming conventions:**
- Use kebab-case: `"add-todo"`, `"search-products"`, `"get-user-profile"`
- Names should be descriptive but concise
- Names are scoped to the page's origin — no global namespace conflicts

```javascript
// Good names
{ name: "add-todo" }
{ name: "search-products" }
{ name: "get-order-status" }

// Avoid — too generic, may conflict
{ name: "search" }
{ name: "add" }
{ name: "get" }
```

### description (required)

| Property | Value |
|---|---|
| **Type** | `DOMString` |
| **Required** | Yes |
| **Purpose** | Natural language explanation for agents |

From the [spec](https://webmachinelearning.github.io/webmcp):

> A natural language description of the tool's functionality. This helps agents
> understand when and how to use the tool.

The description is critical for agent decision-making. It should:
- Explain **what** the tool does
- Explain **when** to use it
- Mention **side effects** (e.g., "This will add an item to the database")
- Be specific enough to differentiate from similar tools

```javascript
// Good descriptions
{ description: "Add a new stamp to the collection. Creates a new record in the database and updates the UI." }
{ description: "Search the product catalog by name, category, or price range. Returns matching products." }
{ description: "Use this tool to purchase a product given its unique product_id. Requires user confirmation." }

// Bad descriptions — too vague
{ description: "Adds stuff" }
{ description: "Does a search" }
```

> **Security Note:** Descriptions are exposed to agents and processed by LLMs. Malicious
> descriptions can be used for prompt injection attacks. See the
> [security considerations](../04-security/) documentation.

### inputSchema (optional)

| Property | Value |
|---|---|
| **Type** | `object` |
| **Required** | No |
| **Purpose** | JSON Schema defining expected input parameters |

From the [spec](https://webmachinelearning.github.io/webmcp):

> A JSON Schema object describing the expected input parameters for the tool.

The `inputSchema` follows [JSON Schema Draft 2020-12](https://json-schema.org/draft/2020-12/json-schema-core.html).

```javascript
{
  inputSchema: {
    type: "object",
    properties: {
      name: { type: "string", description: "The name of the stamp" },
      year: { type: "number", description: "Year the stamp was issued" },
      imageUrl: { type: "string", description: "Optional image URL" }
    },
    required: ["name", "year"]
  }
}
```

When omitted, the tool takes no parameters (the `input` argument to the execute callback
will be an empty object or undefined).

See [input-schema.md](./input-schema.md) for detailed JSON Schema guidance.

### execute (required)

| Property | Value |
|---|---|
| **Type** | `ToolExecuteCallback` |
| **Required** | Yes |
| **Purpose** | JavaScript function invoked when an agent calls the tool |

```webidl
callback ToolExecuteCallback = Promise<any> (object input, ModelContextClient client);
```

From the [spec](https://webmachinelearning.github.io/webmcp):

> A callback function that is invoked when an agent calls the tool. The function
> receives the input parameters and a `ModelContextClient` object. The function can
> be asynchronous and return a promise, in which case the agent will receive the
> result once the promise is resolved.

```javascript
{
  execute: async function({ name, year }, client) {
    addStamp(name, year);
    return {
      content: [
        { type: "text", text: `Stamp "${name}" (${year}) added.` }
      ]
    };
  }
}
```

See [execute-callback.md](./execute-callback.md) for detailed callback documentation.

### annotations (optional)

| Property | Value |
|---|---|
| **Type** | `ToolAnnotations` |
| **Required** | No |
| **Purpose** | Metadata hints about tool behavior |

```webidl
dictionary ToolAnnotations {
  boolean readOnlyHint;
};
```

```javascript
{
  annotations: {
    readOnlyHint: true  // Tells agents this tool only reads data
  }
}
```

See [annotations.md](./annotations.md) for detailed annotation documentation.

## Mapping to MCP Tools Primitive

WebMCP tools align closely with [MCP's](https://modelcontextprotocol.io/introduction) tool primitive. This is by design:

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> This proposal aligns the Web API closely with MCP primitives. This ensures agentic
> capabilities on the Web declared via WebMCP can be used by any MCP compatible Agent
> with minimal translation layers.

| MCP Tool Field | WebMCP Field | Notes |
|---|---|---|
| `name` | `name` | Identical |
| `description` | `description` | Identical |
| `inputSchema` | `inputSchema` | Both use JSON Schema |
| — | `execute` | Web-specific: JS callback (MCP uses protocol messages) |
| `annotations` | `annotations` | Aligned (e.g., `readOnlyHint`) |

### Translation Example

**MCP Server tool definition:**
```json
{
  "name": "add-todo",
  "description": "Add a new todo item",
  "inputSchema": {
    "type": "object",
    "properties": {
      "text": { "type": "string", "description": "Todo text" }
    },
    "required": ["text"]
  }
}
```

**Equivalent WebMCP tool:**
```javascript
{
  name: "add-todo",
  description: "Add a new todo item",
  inputSchema: {
    type: "object",
    properties: {
      text: { type: "string", description: "Todo text" }
    },
    required: ["text"]
  },
  execute: ({ text }) => {
    addTodo(text);
    return { content: [{ type: "text", text: `Added: ${text}` }] };
  }
}
```

The only addition is the `execute` callback, which provides the implementation that
in MCP would be handled on the server side.

## Complete Example

```javascript
navigator.modelContext.provideContext({
  tools: [
    {
      name: "add-stamp",
      description: "Add a new stamp to the collection. Creates a database record and updates the gallery view.",
      inputSchema: {
        type: "object",
        properties: {
          name: {
            type: "string",
            description: "The name of the stamp"
          },
          description: {
            type: "string",
            description: "A brief description of the stamp"
          },
          year: {
            type: "number",
            description: "The year the stamp was issued"
          },
          imageUrl: {
            type: "string",
            description: "An optional image URL for the stamp"
          }
        },
        required: ["name", "description", "year"]
      },
      annotations: {
        readOnlyHint: false
      },
      execute: async function({ name, description, year, imageUrl }, client) {
        addStamp(name, description, year, imageUrl);
        return {
          content: [{
            type: "text",
            text: `Stamp "${name}" added successfully! ` +
                  `The collection now contains ${stamps.length} stamps.`
          }]
        };
      }
    },
    {
      name: "get-stamps",
      description: "Retrieve all stamps currently in the collection. Returns stamp details including name, description, year, and image URL.",
      annotations: {
        readOnlyHint: true
      },
      execute: async function() {
        return {
          content: [{
            type: "text",
            text: JSON.stringify(stamps, null, 2)
          }]
        };
      }
    }
  ]
});
```

## Minimal Tool (No Schema, No Annotations)

The simplest possible tool requires only `name`, `description`, and `execute`:

```javascript
navigator.modelContext.registerTool({
  name: "get-time",
  description: "Get the current date and time",
  execute: () => ({
    content: [{
      type: "text",
      text: new Date().toISOString()
    }]
  })
});
```

## Related Issues

- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Output schema discussion
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — HTML-based tool definitions
- [Issue #53 — Semantic hints for side-effects](https://github.com/webmachinelearning/webmcp/issues/53) — Annotations/readOnlyHint

## See Also

- [registerTool() method](register-tool.md)
- [provideContext() method](provide-context.md)
- [Execute callback specification](execute-callback.md)
- [inputSchema for tool inputs](input-schema.md)
- [outputSchema for structured outputs](output-schema.md)
- [ToolAnnotations metadata](annotations.md)
- [WebIDL definitions](webidl-definitions.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [HTML form to JSON Schema mapping](../03-declarative-api/form-mapping.md)
- [Tool poisoning attacks](../04-security/tool-poisoning.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
