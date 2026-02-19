# victorzxj/webmcp-adapter

> Universal adapter exposing frontend page actions as WebMCP tools with auto-discovery, schema generation, and Service Worker governance.

## Overview

[victorzxj/webmcp-adapter](https://github.com/victorzxj/webmcp-adapter) is a universal adapter that automatically discovers interactive elements on a web page and exposes them as WebMCP tools. Instead of manually registering each tool, the adapter scans the page for actionable elements (forms, buttons, links, inputs) and generates WebMCP tool definitions with proper schemas automatically.

- **Repository**: [github.com/victorzxj/webmcp-adapter](https://github.com/victorzxj/webmcp-adapter)
- **Author**: [@victorzxj](https://github.com/victorzxj)
- **License**: MIT

## Key Features

### Auto-Discovery

Automatically scans the page DOM to discover interactive elements:

```javascript
import { WebMCPAdapter } from 'webmcp-adapter';

const adapter = new WebMCPAdapter();

// Auto-discover all interactive elements and register as tools
adapter.discover();

// Or configure which elements to discover
adapter.discover({
  forms: true,         // HTML forms → tools
  buttons: true,       // Buttons → tools
  links: false,        // Skip links
  inputs: true,        // Standalone inputs → tools
  customSelectors: [   // Custom CSS selectors
    "[data-action]",
    ".interactive-widget"
  ]
});
```

### Schema Generation

Automatically generates JSON Schema from discovered elements:

```html
<!-- This form on the page... -->
<form id="search" action="/search">
  <input type="text" name="query" required placeholder="Search...">
  <select name="category">
    <option value="all">All</option>
    <option value="books">Books</option>
    <option value="electronics">Electronics</option>
  </select>
  <input type="number" name="maxPrice" min="0" max="10000">
  <button type="submit">Search</button>
</form>
```

```javascript
// ...automatically becomes this WebMCP tool:
{
  name: "search_form",
  description: "Submit the search form",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search..." },
      category: {
        type: "string",
        enum: ["all", "books", "electronics"]
      },
      maxPrice: {
        type: "number",
        minimum: 0,
        maximum: 10000
      }
    },
    required: ["query"]
  }
}
```

### Service Worker Governance

Leverages [Service Workers](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) for tool governance and lifecycle management:

```javascript
// service-worker.js
import { WebMCPGovernor } from 'webmcp-adapter/sw';

const governor = new WebMCPGovernor();

governor.configure({
  // Rate limiting per tool
  rateLimits: {
    "search_form": { maxCalls: 10, windowMs: 60000 },
    "checkout_form": { maxCalls: 1, windowMs: 300000 }
  },
  // Tool access control
  accessControl: {
    "delete_account": { requireUserInteraction: true },
    "purchase_item": { requireUserInteraction: true }
  },
  // Audit logging
  logging: {
    enabled: true,
    destination: "/api/audit-log"
  }
});
```

### Custom Tool Definitions

Override auto-discovered tools with custom definitions:

```javascript
adapter.override("search_form", {
  name: "searchProducts",
  description: "Search the product catalog by keyword and category",
  annotations: {
    readOnlyHint: true
  },
  execute: async (params) => {
    // Custom execution logic instead of form submission
    const results = await fetch(`/api/search?q=${params.query}`);
    return { content: [{ type: "text", text: await results.text() }] };
  }
});
```

## Installation

```bash
npm install webmcp-adapter
```

## Quick Start

### Minimal Setup

```javascript
import { WebMCPAdapter } from 'webmcp-adapter';

// One line: discover all interactive elements and register as WebMCP tools
new WebMCPAdapter().discover();
```

### With Configuration

```javascript
import { WebMCPAdapter } from 'webmcp-adapter';

const adapter = new WebMCPAdapter({
  // Naming strategy for generated tools
  naming: "semantic",  // "semantic" | "id-based" | "path-based"

  // Description generation
  descriptions: "auto", // "auto" | "aria-labels" | "none"

  // Annotations
  defaultAnnotations: {
    readOnlyHint: false
  },

  // Filter elements
  exclude: [
    "#admin-panel",    // Exclude by CSS selector
    "[data-internal]"  // Exclude internal tools
  ]
});

adapter.discover();

// List discovered tools
console.log(adapter.getTools());
// [
//   { name: "searchProducts", description: "Search the product catalog", ... },
//   { name: "addToCart", description: "Add item to shopping cart", ... },
//   { name: "contactForm", description: "Submit contact inquiry", ... }
// ]
```

## Architecture

```
┌──────────────────────────────────────────┐
│  Web Page                                │
│                                          │
│  ┌──────────────┐  ┌──────────────────┐  │
│  │  Forms       │  │  Buttons/Links   │  │
│  │  <form>      │  │  <button>        │  │
│  │  <input>     │  │  <a>             │  │
│  └──────┬───────┘  └──────┬───────────┘  │
│         │                 │              │
│  ┌──────▼─────────────────▼───────────┐  │
│  │  WebMCP Adapter                    │  │
│  │  - DOM Scanner                     │  │
│  │  - Schema Generator                │  │
│  │  - Tool Registrar                  │  │
│  └──────┬─────────────────────────────┘  │
│         │                                │
│  ┌──────▼─────────────────────────────┐  │
│  │  navigator.modelContext            │  │
│  │  (Native or Polyfill)              │  │
│  └────────────────────────────────────┘  │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │  Service Worker (Governor)         │  │
│  │  - Rate limiting                   │  │
│  │  - Access control                  │  │
│  │  - Audit logging                   │  │
│  └────────────────────────────────────┘  │
└──────────────────────────────────────────┘
```

## Naming Strategies

| Strategy | Example Element | Generated Name |
|----------|----------------|----------------|
| `semantic` | `<form id="search" action="/search">` | `searchProducts` |
| `id-based` | `<form id="search-form">` | `search_form` |
| `path-based` | `<form action="/api/search">` | `api_search_form` |

## Use Cases

- **Legacy sites**: Add WebMCP support to existing sites without code changes
- **Rapid prototyping**: Quickly expose page actions for AI testing
- **Accessibility bridge**: Expose interactive elements to AI for accessibility assistance
- **Migration path**: Start with auto-discovery, then refine with custom definitions

## See Also

- [WebMCP Bridge CLI](./webmcp-bridge-cli.md) — Bridge page tools to MCP clients
- [three-water666/WebMCP](./three-water-webmcp.md) — Universal bridge for web AI
- [OpenClaw Skill](./openclaw-skill.md) — AI agent skill for WebMCP tools
- [Service Workers Spec](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) — Background tool execution
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [WebMCP vs server-side MCP](../../01-overview/webmcp-vs-server-mcp.md) — WebMCP vs server-side MCP
- [MCP protocol intersection](../../02-specification/mcp-intersection.md) — MCP protocol intersection
- [MCP protocol alignment](../../07-architecture/mcp-protocol-alignment.md) — MCP protocol alignment
- [SDK abstraction architecture](../../07-architecture/sdk-abstraction.md) — SDK abstraction architecture
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
