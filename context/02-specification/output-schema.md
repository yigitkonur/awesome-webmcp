# outputSchema — Structured Output Definitions

## Overview

The `outputSchema` concept addresses how tools return structured results to agents.
While not yet a required part of the [WebMCP spec](https://webmachinelearning.github.io/webmcp), it was discussed in
[Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9)
(opened August 2025, discussions continued into February 2026). This document covers
the current output format and the outputSchema proposal.

## Current Output Format

Tools return results as JavaScript objects. The established convention from the spec
examples uses a `content` array pattern aligned with the [MCP specification](https://modelcontextprotocol.io/specification/latest):

```javascript
execute: async ({ name, year }) => {
  addStamp(name, year);
  return {
    content: [
      {
        type: "text",
        text: `Stamp "${name}" added successfully! The collection now contains ${stamps.length} stamps.`
      }
    ]
  };
}
```

### Content Array Structure

The return value follows MCP's content structure:

```javascript
{
  content: [
    {
      type: "text",     // Content type identifier
      text: "..."       // The actual content
    }
  ]
}
```

### Supported Content Types

Based on the spec examples and MCP alignment:

| Type | Description | Example |
|---|---|---|
| `"text"` | Plain text or JSON string | Status messages, serialized data |

### Multi-Item Content

A single tool response can include multiple content items:

```javascript
return {
  content: [
    { type: "text", text: "Operation completed successfully." },
    { type: "text", text: JSON.stringify(resultData) }
  ]
};
```

## Issue #9 — outputSchema Proposal

[Issue #9](https://github.com/webmachinelearning/webmcp/issues/9) was opened by
David Bokan (Google) in August 2025, with input from participants including
[Khushal Sagar](https://github.com/khushalsagar), [Mark Foltz](https://github.com/markafoltz),
and [Brandon Walderman](https://github.com/bwalderman):

### Context: MCP's outputSchema

The [MCP specification](https://modelcontextprotocol.io/specification/latest) added `outputSchema` to its tool definition in the June 2025 specification revision,
developed by [Anthropic](https://www.anthropic.com/).
This allows MCP servers to declare the structure of their tool responses using
[JSON Schema 2020-12](https://json-schema.org/draft/2020-12/json-schema-core.html).

### Arguments For outputSchema in WebMCP

1. **Agent comprehension** — Descriptions in the output schema help agents understand
   what each field in the response means
2. **Validation** — Agents can validate tool responses against the declared schema
3. **MCP alignment** — Keeps WebMCP compatible with MCP tooling
4. **Code generation** — Agents could generate typed interfaces from the schema
5. **Documentation** — Serves as self-documenting API contract

### Arguments Against

1. **Redundancy** — The return value is already a JavaScript object; agents can
   inspect it directly
2. **Maintenance burden** — Another schema to keep in sync with implementation
3. **Complexity** — More surface area for web developers to manage

### Proposed WebIDL Addition

If adopted, `outputSchema` would be added to `ModelContextTool` (see also [Issue #41](https://github.com/webmachinelearning/webmcp/issues/41)
and [Issue #81](https://github.com/webmachinelearning/webmcp/issues/81) for related schema discussions):

```webidl
dictionary ModelContextTool {
  required DOMString name;
  required DOMString description;
  object inputSchema;
  object outputSchema;          // Proposed addition
  required ToolExecuteCallback execute;
  ToolAnnotations annotations;
};
```

### Usage Example

The schema definitions follow [JSON Schema 2020-12](https://json-schema.org/draft/2020-12/json-schema-core.html) conventions:

```javascript
navigator.modelContext.registerTool({
  name: "search-products",
  description: "Search the product catalog",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search query" }
    },
    required: ["query"]
  },
  outputSchema: {
    type: "object",
    properties: {
      content: {
        type: "array",
        items: {
          type: "object",
          properties: {
            type: { type: "string", enum: ["text"] },
            text: { type: "string", description: "JSON-serialized array of product objects" }
          }
        }
      }
    },
    description: "Returns a content array with product search results as JSON text"
  },
  execute: async ({ query }) => {
    const results = await searchProducts(query);
    return {
      content: [{
        type: "text",
        text: JSON.stringify(results)
      }]
    };
  }
});
```

## Multi-Modal Output

The [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md) document mentions that web platform integration enables multi-modal
output beyond text:

> The API ergonomics can align with the Web platform. For example, tool response can
> use `img` or `video` elements for multi-modal output.

### Potential Future Content Types

| Type | Description | Potential Format |
|---|---|---|
| `"text"` | Plain text / JSON | `{ type: "text", text: "..." }` |
| `"image"` | Image data | `{ type: "image", element: HTMLImageElement }` |
| `"video"` | Video content | `{ type: "video", element: HTMLVideoElement }` |
| `"html"` | Rich HTML | `{ type: "html", content: "..." }` |

### Image Output Example (Speculative)

```javascript
execute: async ({ productId }) => {
  const product = await getProduct(productId);
  const img = new Image();
  img.src = product.imageUrl;
  await img.decode();

  return {
    content: [
      { type: "text", text: `${product.name} - $${product.price}` },
      { type: "image", element: img }  // Multi-modal output
    ]
  };
}
```

## Error Responses

When a tool execution fails, the convention is to throw an error:

```javascript
execute: async ({ productId }) => {
  const product = await getProduct(productId);
  if (!product) {
    throw new Error(`Product ${productId} not found`);
  }
  return {
    content: [{ type: "text", text: JSON.stringify(product) }]
  };
}
```

### Error Schema Discussion

From Issue #48 (related to abort/cancellation):

> If we decide to go with [Issue #9] then maybe we need defined error values. So the
> author can either return a value based on the output schema (success) or one of the
> error codes (failure/abort).

Potential error response structure:

```javascript
// Success path
return {
  content: [{ type: "text", text: "Success" }]
};

// Error path — thrown errors are caught by the browser
throw new Error("Product not found");

// Or structured error (proposed)
return {
  isError: true,
  content: [{ type: "text", text: "Product not found" }]
};
```

## Current Best Practices

Until `outputSchema` is formally adopted:

### 1. Use the content array pattern

```javascript
return {
  content: [
    { type: "text", text: "Your structured response here" }
  ]
};
```

### 2. Serialize complex data as JSON text

```javascript
return {
  content: [{
    type: "text",
    text: JSON.stringify({
      stamps: stamps.map(s => ({ name: s.name, year: s.year })),
      totalCount: stamps.length
    })
  }]
};
```

### 3. Include descriptive text alongside data

```javascript
return {
  content: [
    { type: "text", text: `Found ${results.length} matching products.` },
    { type: "text", text: JSON.stringify(results) }
  ]
};
```

### 4. Document output format in the tool description

```javascript
{
  name: "get-stamps",
  description: "Retrieve all stamps. Returns JSON array with objects containing: name (string), description (string), year (number), imageUrl (string|null).",
  execute: async () => ({
    content: [{ type: "text", text: JSON.stringify(stamps) }]
  })
}
```

## Related Issues

- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Main discussion
- [Issue #41](https://github.com/webmachinelearning/webmcp/issues/41) — Schema definition alignment
- [Issue #48 — Add a callback for abort](https://github.com/webmachinelearning/webmcp/issues/48) — Error codes tied to outputSchema
- [Issue #81](https://github.com/webmachinelearning/webmcp/issues/81) — Structured response validation
- [Issue #82](https://github.com/webmachinelearning/webmcp/issues/82) — Output type extensions
- [Issue #29](https://github.com/webmachinelearning/webmcp/issues/29) — Content type negotiation
- [PR #75](https://github.com/webmachinelearning/webmcp/pull/75) — outputSchema implementation draft

### Discussion Participants

Key contributors to the outputSchema discussions include
[David Pang](https://github.com/dsp-ant), [Alex Nahas](https://github.com/MiguelsPizza),
and [Jason McGhee](https://github.com/jasonjmcghee), with feedback from
[OpenAI](https://openai.com/) and [Anthropic](https://www.anthropic.com/) representatives.
Discussion details are tracked in the [telcons](https://github.com/webmachinelearning/meetings).

## See Also

- [inputSchema for tool inputs](input-schema.md)
- [Tool registration interface](tool-registration-interface.md)
- [Execute callback specification](execute-callback.md)
- [registerTool() method](register-tool.md)
- [ToolAnnotations metadata](annotations.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Output injection attacks](../04-security/output-injection.md)
- [HTML form to JSON Schema mapping](../03-declarative-api/form-mapping.md)
- [API reference](../15-reference/api-reference.md)
