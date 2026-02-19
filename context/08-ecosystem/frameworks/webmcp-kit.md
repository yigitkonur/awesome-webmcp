# victorhuangwq/webmcp-kit

> "The easiest way to add WebMCP tools to your website." — Lightweight toolkit for rapid WebMCP integration.

## Overview

[victorhuangwq/webmcp-kit](https://github.com/victorhuangwq/webmcp-kit) is a lightweight, framework-agnostic toolkit created by **Victor Huang** ([@victorhuangwq](https://github.com/victorhuangwq)), a Google engineer who also contributes to the [W3C WebMCP spec](https://webmachinelearning.github.io/webmcp) (Security & Privacy, tool annotations). WebMCP Kit aims to be the simplest way to add WebMCP tools to any website with minimal configuration.

- **Repository**: [github.com/victorhuangwq/webmcp-kit](https://github.com/victorhuangwq/webmcp-kit)
- **Author**: Victor Huang (Google, W3C WebMCP spec contributor)
- **License**: MIT

## Key Features

### Minimal API

WebMCP Kit provides a simplified wrapper around the native `navigator.modelContext` API:

```javascript
import { defineTools } from 'webmcp-kit';

defineTools([
  {
    name: "search",
    description: "Search for items",
    params: {
      query: { type: "string", required: true },
      limit: { type: "number", default: 10 }
    },
    handler: async ({ query, limit }) => {
      const results = await searchAPI(query, limit);
      return results;
    }
  },
  {
    name: "getDetails",
    description: "Get item details by ID",
    params: {
      id: { type: "string", required: true }
    },
    readOnly: true,
    handler: async ({ id }) => {
      return await getItemById(id);
    }
  }
]);
```

### Simplified Parameter Definition

Instead of full JSON Schema, WebMCP Kit uses a simplified parameter syntax that auto-generates the correct schema:

```javascript
// WebMCP Kit simplified syntax
{
  params: {
    query: { type: "string", required: true, description: "Search query" },
    category: { type: "string", enum: ["books", "electronics", "clothing"] },
    maxPrice: { type: "number", min: 0 },
    inStock: { type: "boolean", default: true }
  }
}

// Auto-generates JSON Schema:
{
  type: "object",
  properties: {
    query: { type: "string", description: "Search query" },
    category: { type: "string", enum: ["books", "electronics", "clothing"] },
    maxPrice: { type: "number", minimum: 0 },
    inStock: { type: "boolean" }
  },
  required: ["query"]
}
```

### Auto Annotations

Annotation shortcuts for common patterns:

```javascript
defineTools([
  {
    name: "getUser",
    description: "Get user profile",
    readOnly: true,     // Sets readOnlyHint: true
    // ...
  },
  {
    name: "deleteUser",
    description: "Delete a user account",
    destructive: true,  // Sets destructiveHint: true
    confirmRequired: true, // Uses requestUserInteraction
    // ...
  },
  {
    name: "updateSettings",
    description: "Update user settings",
    idempotent: true,   // Sets idempotentHint: true
    // ...
  }
]);
```

### Declarative Mode

Auto-scan forms for WebMCP attributes:

```javascript
import { scanForms } from 'webmcp-kit';

// Automatically registers tools from HTML forms with toolname attributes
scanForms();
```

```html
<form toolname="subscribe" tooldescription="Subscribe to newsletter">
  <input type="email" name="email" required
         toolparamdescription="Email address">
  <button type="submit">Subscribe</button>
</form>
```

### Event Helpers

Simplified event handling:

```javascript
import { onToolActivated, onToolCancel } from 'webmcp-kit';

onToolActivated((toolName, params) => {
  console.log(`Tool ${toolName} activated with`, params);
  showLoadingIndicator(toolName);
});

onToolCancel((toolName) => {
  console.log(`Tool ${toolName} cancelled`);
  hideLoadingIndicator(toolName);
});
```

## Installation

### npm

```bash
npm install webmcp-kit
```

### CDN

```html
<script src="https://unpkg.com/webmcp-kit/dist/webmcp-kit.min.js"></script>
<script>
  WebMCPKit.defineTools([/* ... */]);
</script>
```

## Comparison with Direct API

| Feature | webmcp-kit | Direct navigator.modelContext |
|---------|-----------|-------------------------------|
| Parameter definition | Simplified syntax | Full JSON Schema |
| Annotations | Shorthand flags | Manual annotation object |
| Response formatting | Auto-wrapped | Manual content array |
| Form scanning | Built-in `scanForms()` | Manual attribute reading |
| Event handling | Callback helpers | addEventListener |
| Feature detection | Built-in | Manual check |
| Polyfill inclusion | Optional auto-include | Manual import |

## Quick Start

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://unpkg.com/webmcp-kit/dist/webmcp-kit.min.js"></script>
</head>
<body>
  <h1>My WebMCP App</h1>

  <script>
    WebMCPKit.defineTools([
      {
        name: "greet",
        description: "Greet a user by name",
        params: {
          name: { type: "string", required: true }
        },
        readOnly: true,
        handler: async ({ name }) => `Hello, ${name}! Welcome to our site.`
      }
    ]);
  </script>
</body>
</html>
```

## See Also

- [@mcp-b/react-webmcp](./react-webmcp.md) — React-specific hooks
- [webmcp-rails](./webmcp-rails.md) — Ruby on Rails integration
- [@mcp-b/global Polyfill](../polyfills/mcp-b-global.md) — WebMCP polyfill
- [DoorDash Starter](../demos/doordash-starter.md) — Single-file demo with zero deps
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Tool Annotations (Issue #53)](https://github.com/webmachinelearning/webmcp/issues/53) — Spec discussion
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Input schema specification](../../02-specification/input-schema.md) — Input schema specification
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [SDK abstraction architecture](../../07-architecture/sdk-abstraction.md) — SDK abstraction architecture
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Best practices](../../12-tutorials/best-practices.md) — Best practices
- [Enterprise workflows use case](../../11-use-cases/enterprise-workflows.md) — Enterprise workflows use case
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [JSON schema patterns](../../15-reference/json-schema-patterns.md) — JSON schema patterns
