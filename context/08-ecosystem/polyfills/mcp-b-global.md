# @mcp-b/global — WebMCP Polyfill

> Drop-in polyfill implementing `navigator.modelContext` for browsers without native WebMCP support.

## Overview

[@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global) is an npm package that polyfills the `navigator.modelContext` API defined in the [W3C WebMCP specification](https://webmachinelearning.github.io/webmcp). It provides a complete implementation of the WebMCP API surface for browsers that do not yet have native support (e.g., Firefox, Safari, or older Chrome versions).

- **npm**: [@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global)
- **Source**: [WebMCP-org/npm-packages](https://github.com/WebMCP-org/npm-packages) (originally developed in the [MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP) monorepo, npm packages later split into a separate repo)
- **License**: MIT (note: the parent MiguelsPizza/WebMCP monorepo is AGPL-3.0, but the `@mcp-b/global` npm package is MIT-licensed)
- **Latest Version**: 1.5.0 (as of Feb 2026, 50 versions published)
- **Bundle Size**: ~285KB (IIFE)
- **Maintained by**: WebMCP-org (originally by Alex Nahas / [@MiguelsPizza](https://github.com/MiguelsPizza))

## Key Features

- **Full API surface**: Implements `registerTool()`, `provideContext()`, `unregisterTool()`, `clearContext()`, and all spec-defined methods
- **Drop-in IIFE**: Add AI capabilities with a single `<script>` tag — no build step required
- **Native Chromium detection**: Auto-detects and uses native browser implementation when available
- **Dual Transport**: Works with both same-window clients AND parent pages (iframe support)
- **Two-Bucket System**: Manage app-level and component-level tools separately
- **Feature detection compatible**: Works with the standard `"modelContext" in navigator` check
- **Event support**: Fires `toolactivated`, `toolcancel`, and other spec-defined events
- **Tool annotations**: Supports `readOnlyHint`, `destructiveHint`, `idempotentHint` annotations
- **JSON Schema validation**: Validates tool input against `inputSchema` definitions
- **Declarative API support**: Detects `toolname`, `tooldescription`, `toolparamdescription` HTML attributes

## Installation

### npm

```bash
npm install @mcp-b/global
```

### pnpm

```bash
pnpm add @mcp-b/global
```

### yarn

```bash
yarn add @mcp-b/global
```

### CDN / Script Tag

```html
<script src="https://unpkg.com/@mcp-b/global/dist/index.global.js"></script>
```

## Usage

### Basic Setup

Import the polyfill at the top of your application entry point. It automatically installs `navigator.modelContext` if not already present:

```javascript
// Import the polyfill (auto-installs on navigator)
import '@mcp-b/global';

// Now navigator.modelContext is available
navigator.modelContext.registerTool({
  name: "searchProducts",
  description: "Search for products in the catalog",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search query" },
      category: { type: "string", description: "Product category" },
      maxResults: { type: "number", description: "Maximum results to return" }
    },
    required: ["query"]
  },
  execute: async ({ query, category, maxResults }) => {
    const results = await fetchProducts(query, category, maxResults);
    return {
      content: [{ type: "text", text: JSON.stringify(results) }]
    };
  }
});
```

### Feature Detection

The polyfill respects the standard feature detection pattern:

```javascript
import '@mcp-b/global';

if ("modelContext" in navigator) {
  // WebMCP is available (either native or polyfilled)
  navigator.modelContext.registerTool({ /* ... */ });
} else {
  console.log("WebMCP not available");
}
```

### With Declarative HTML

The polyfill also supports the declarative API:

```html
<script src="https://unpkg.com/@mcp-b/global/dist/index.global.js"></script>

<form toolname="contact_us"
      tooldescription="Submit a contact form inquiry"
      action="/api/contact">
  <input type="text" name="name" required
         toolparamdescription="Your full name">
  <input type="email" name="email" required
         toolparamdescription="Your email address">
  <textarea name="message" required
            toolparamdescription="Your message"></textarea>
  <button type="submit">Send</button>
</form>
```

### Using provideContext()

```javascript
import '@mcp-b/global';

navigator.modelContext.provideContext({
  tools: [
    {
      name: "getWeather",
      description: "Get current weather for a location",
      inputSchema: {
        type: "object",
        properties: {
          city: { type: "string" }
        },
        required: ["city"]
      },
      execute: async ({ city }) => {
        const weather = await fetchWeather(city);
        return { content: [{ type: "text", text: JSON.stringify(weather) }] };
      }
    },
    {
      name: "getNews",
      description: "Get latest news headlines",
      inputSchema: { type: "object", properties: {} },
      execute: async () => {
        const news = await fetchNews();
        return { content: [{ type: "text", text: JSON.stringify(news) }] };
      }
    }
  ]
});
```

### Removing Tools

```javascript
// Remove a specific tool
navigator.modelContext.unregisterTool("searchProducts");

// Clear all tools and context
navigator.modelContext.clearContext();
```

## API Reference

### `navigator.modelContext.registerTool(toolDefinition)`

Registers a single tool with the polyfilled model context.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | `string` | ✅ | Unique tool identifier |
| `description` | `string` | ✅ | Human-readable description for AI agents |
| `inputSchema` | `object` | ✅ | JSON Schema defining tool parameters |
| `outputSchema` | `object` | ❌ | JSON Schema defining response structure |
| `execute` | `function` | ✅ | Async handler receiving validated parameters |
| `annotations` | `object` | ❌ | Hints: `readOnlyHint`, `destructiveHint`, `idempotentHint` |

### `navigator.modelContext.provideContext(context)`

Replaces all registered tools with the provided set.

### `navigator.modelContext.unregisterTool(name)`

Removes a registered tool by name.

### `navigator.modelContext.clearContext()`

Removes all registered tools and context.

## How It Differs from Native

| Aspect | Native (Chrome 146+) | Polyfill (@mcp-b/global) |
|--------|----------------------|--------------------------|
| Browser support | Chrome Canary only | All modern browsers |
| AI agent integration | Built into Chrome's agent | Requires MCP-B extension or bridge |
| Performance | Native speed | JavaScript overhead |
| Security model | Browser-enforced | Application-level |
| Declarative API | Full support | Partial (form detection) |
| CSS pseudo-classes | `:tool-form-active` etc. | Not supported |
| Service Workers | Supported | Not supported |

## Compatibility

- **Browsers**: Chrome 90+, Firefox 90+, Safari 15+, Edge 90+
- **Node.js**: Not applicable (browser-only API)
- **Bundlers**: Webpack, Vite, Rollup, esbuild
- **Frameworks**: Works with React, Vue, Angular, Svelte, and vanilla JS

## See Also

- [MiguelsPizza/WebMCP](./miguels-pizza-webmcp.md) — Parent MCP-B project
- [@mcp-b/react-webmcp](../frameworks/react-webmcp.md) — React hooks for WebMCP
- [ripulio/web-mcp](./ripulio-web-mcp.md) — Alternative polyfill implementation
- [opentiny/next-sdk](./opentiny-next-sdk.md) — Frontend AI SDK with WebMCP
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Navigator model context API](../../02-specification/navigator-model-context.md) — Navigator model context API
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Polyfill usage guide](../../12-tutorials/polyfill-guide.md) — Polyfill usage guide
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Browser support status](../../01-overview/browser-support.md) — Browser support status
- [Mozilla's position on WebMCP](../../14-industry/mozilla-position.md) — Mozilla's position on WebMCP
- [Safari support status](../../14-industry/apple-safari-status.md) — Safari support status
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
