# opentiny/next-sdk

> Frontend AI SDK with WebMcpServer/WebMcpClient classes, transport adapters, Zod validation, and Vue 3 chat UI component.

## Overview

[opentiny/next-sdk](https://github.com/opentiny/next-sdk) is a frontend AI SDK by the **OpenTiny** team (Huawei) that enables defining MCP servers directly in the frontend, allowing AI to operate web applications using natural language. It provides `WebMcpServer` and `WebMcpClient` classes with multiple transport adapters (MessageChannel, SSE, HTTP), Zod-based schema validation, and a Vue 3 chat UI component.

- **Repository**: [github.com/opentiny/next-sdk](https://github.com/opentiny/next-sdk)
- **Documentation**: [docs.opentiny.design/next-sdk/guide](https://docs.opentiny.design/next-sdk/guide)
- **Stars**: ~6 (as of Feb 2026)
- **License**: MIT
- **Language**: TypeScript
- **Package Manager**: pnpm

## Key Features

### WebMcpServer

The server-side component that runs in the browser, exposing tools to AI agents:

```typescript
import { WebMcpServer } from '@opentiny/next-sdk';

const server = new WebMcpServer({
  name: "my-app",
  version: "1.0.0"
});

server.registerTool({
  name: "searchProducts",
  description: "Search products in the catalog",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string" },
      category: { type: "string" }
    },
    required: ["query"]
  },
  handler: async ({ query, category }) => {
    const results = await searchAPI(query, category);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});

server.start();
```

### WebMcpClient

The client-side component that discovers and invokes tools:

```typescript
import { WebMcpClient } from '@opentiny/next-sdk';

const client = new WebMcpClient();
await client.connect();

// Discover available tools
const tools = await client.listTools();

// Invoke a tool
const result = await client.callTool("searchProducts", {
  query: "laptop",
  category: "electronics"
});
```

### Transport Adapters

Multiple communication channels between server and client:

| Transport | Use Case | Protocol |
|-----------|----------|----------|
| **MessageChannel** | Same-page communication | `postMessage` / `MessageChannel` API |
| **SSE (Server-Sent Events)** | Server-push updates | HTTP streaming |
| **HTTP** | Traditional request-response | REST endpoints |

```typescript
import { MessageChannelTransport, SSETransport, HTTPTransport } from '@opentiny/next-sdk';

// MessageChannel transport (in-browser)
const mcTransport = new MessageChannelTransport();

// SSE transport (server-push)
const sseTransport = new SSETransport({ url: "/api/mcp/sse" });

// HTTP transport (REST)
const httpTransport = new HTTPTransport({ baseUrl: "/api/mcp" });

const server = new WebMcpServer({
  transport: mcTransport // or sseTransport, httpTransport
});
```

### Zod Validation

Built-in Zod schema validation for type-safe tool parameters:

```typescript
import { z } from 'zod';
import { WebMcpServer } from '@opentiny/next-sdk';

const server = new WebMcpServer();

server.registerTool({
  name: "createOrder",
  description: "Create a new order",
  schema: z.object({
    productId: z.string().uuid(),
    quantity: z.number().int().positive(),
    shippingAddress: z.object({
      street: z.string(),
      city: z.string(),
      zip: z.string().regex(/^\d{5}$/)
    })
  }),
  handler: async (params) => {
    // params are fully typed and validated
    const order = await createOrder(params);
    return { content: [{ type: "text", text: JSON.stringify(order) }] };
  }
});
```

### Vue 3 Chat UI Component (next-remoter)

A Vue 3 composable AI chat component for interactive tool invocation:

```vue
<template>
  <NextRemoter
    :server="mcpServer"
    :llm-config="llmConfig"
    placeholder="Ask me anything..."
  />
</template>

<script setup>
import { NextRemoter } from '@opentiny/next-remoter';
import { WebMcpServer } from '@opentiny/next-sdk';

const mcpServer = new WebMcpServer();
const llmConfig = {
  provider: "openai",
  model: "gpt-4",
  apiKey: "your-key"
};
</script>
```

## Project Structure

```
next-sdk/
├── packages/
│   ├── next-sdk/              # Core SDK package
│   │   ├── agent/             # WebAgent implementation
│   │   ├── client/            # WebMCP client
│   │   ├── server/            # WebMCP server
│   │   ├── transport/         # Transport layer implementations
│   │   ├── McpSdk.ts          # MCP SDK wrapper
│   │   ├── index.ts           # Main entry point
│   │   └── package.json
│   ├── next-remoter/          # Vue 3 AI chat component
│   │   ├── src/
│   │   │   ├── components/    # UI components
│   │   │   └── composable/    # Vue composables
│   │   └── package.json
│   └── doc-ai/                # Documentation example app
├── docs/                      # Project documentation
├── pnpm-workspace.yaml
└── README.md
```

## Installation

```bash
# Clone the repository
git clone https://github.com/opentiny/next-sdk.git
cd next-sdk

# Install dependencies
pnpm install

# Development mode (hot reload)
cd packages/next-sdk
pnpm dev

# Build all packages
pnpm build
```

### Install via npm

```bash
# Core SDK
npm install @opentiny/next-sdk

# Vue 3 chat component
npm install @opentiny/next-remoter
```

## Architecture

```
┌─────────────────────────────────────────────┐
│  AI Agent / LLM                             │
│  (OpenAI, Gemini, etc.)                     │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  WebMcpClient                               │
│  - listTools(), callTool()                  │
│  - Transport adapter (MC, SSE, HTTP)        │
└──────────────┬──────────────────────────────┘
               │ MessageChannel / SSE / HTTP
┌──────────────▼──────────────────────────────┐
│  WebMcpServer                               │
│  - registerTool()                           │
│  - Zod validation                           │
│  - Handler execution                        │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Web Application                            │
│  - Frontend logic                           │
│  - API calls                                │
│  - DOM manipulation                         │
└─────────────────────────────────────────────┘
```

## Comparison with Other SDKs

| Feature | opentiny/next-sdk | @mcp-b/global | ripulio/web-mcp |
|---------|-------------------|---------------|-----------------|
| Server/Client classes | ✅ | ❌ (polyfill only) | ❌ (CLI-based) |
| Transport adapters | MC, SSE, HTTP | N/A | CDP |
| Zod validation | ✅ | ❌ | ❌ |
| Chat UI component | ✅ (Vue 3) | ❌ | ❌ |
| Framework focus | Vue 3 | Framework-agnostic | Framework-agnostic |
| Documentation site | ✅ | ❌ | ❌ |

## Requirements

- Node.js 18+
- pnpm 8+
- Vue 3 (for next-remoter component)
- TypeScript 5+

## See Also

- [@mcp-b/global](./mcp-b-global.md) — WebMCP polyfill
- [MiguelsPizza/WebMCP](./miguels-pizza-webmcp.md) — MCP-B reference implementation
- [ripulio/web-mcp](./ripulio-web-mcp.md) — Alternative WebMCP implementation
- [@mcp-b/react-webmcp](../frameworks/react-webmcp.md) — React hooks for WebMCP
- [OpenTiny Documentation](https://docs.opentiny.design/next-sdk/guide) — Official docs
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Navigator model context interface](../../02-specification/navigator-model-context.md) — Navigator model context interface
- [MCP protocol intersection](../../02-specification/mcp-intersection.md) — MCP protocol intersection
- [Polyfill usage guide](../../12-tutorials/polyfill-guide.md) — Polyfill usage guide
- [Browser support status](../../01-overview/browser-support.md) — Browser support status
- [SDK abstraction layer](../../07-architecture/sdk-abstraction.md) — SDK abstraction layer
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Community contributors](../../09-people/other-contributors.md) — Community contributors
