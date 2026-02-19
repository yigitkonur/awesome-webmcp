# MiguelsPizza/WebMCP (MCP-B)

> MCP-B reference implementation — the project that inspired the W3C WebMCP proposal. Monorepo with Chrome extension, transports, native-server bridge, and Zod validation.

## Overview

[MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP) is the **MCP-B (Model Context Protocol for the Browser)** reference implementation created by **Alex Nahas** ([@MiguelsPizza](https://github.com/MiguelsPizza)), an Amazon engineer whose work directly inspired the [W3C WebMCP proposal](https://github.com/webmachinelearning/webmcp/pull/26). MCP-B extends the Model Context Protocol to the browser, enabling web applications to safely expose tools and context to AI agents.

- **Repository**: [github.com/MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP)
- **npm packages**: Now published from [WebMCP-org/npm-packages](https://github.com/WebMCP-org/npm-packages) (MIT license)
- **Website**: [mcp-b.ai](https://mcp-b.ai)
- **Documentation**: [docs.mcp-b.ai](https://docs.mcp-b.ai)
- **Stars**: ~998 (as of Feb 2026)
- **Forks**: ~71
- **License**: AGPL-3.0 (monorepo); npm packages under MIT
- **Chrome Extension**: [MCP-B Extension](https://chromewebstore.google.com/detail/mcp-b/daohopfhkdelnpemnhlekblhnikhdhfa)
- **Discord**: [Join community](https://discord.gg/a9fBR6Bw)

## Historical Significance

Alex Nahas created MCP-B as a way to bring the server-side Model Context Protocol (MCP) into the browser. His work caught the attention of Google and Microsoft engineers, leading to the formal W3C WebMCP proposal. Key contributions:

- Filed the original [declarative proposal (PR #26)](https://github.com/webmachinelearning/webmcp/pull/26) to the W3C spec
- Raised [async registration (issue #30)](https://github.com/webmachinelearning/webmcp/issues/30) for dynamic tool loading
- Featured in [Arcade.dev interview](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview) about the creation of MCP-B
- The project is now continued under the [WebMCP-org](https://github.com/WebMCP-org) GitHub organization

## Monorepo Structure

> **Note**: The npm packages have been split out to a dedicated [WebMCP-org/npm-packages](https://github.com/WebMCP-org/npm-packages) repository (MIT license), while the main MiguelsPizza/WebMCP repo (AGPL-3.0) contains the Chrome extension and architecture docs.

```
WebMCP/
├── packages/
│   ├── @mcp-b/global/          # navigator.modelContext polyfill
│   ├── @mcp-b/transports/      # Transport layer (tab, extension, bridge)
│   ├── @mcp-b/react-webmcp/    # React hooks for WebMCP
│   ├── @mcp-b/chrome-extension/ # Chrome extension (MCP-B client)
│   ├── @mcp-b/native-server/   # Bridge to native MCP servers
│   └── @mcp-b/chrome-devtools-mcp/ # Chrome DevTools MCP integration
├── examples/                   # Demo applications
│   ├── vanilla-ts/             # Vanilla TypeScript todo app
│   ├── react-demo/             # React integration demo
│   └── script-tag/             # Simplest integration (script tag)
├── docs/                       # Documentation
└── FullArch.png                # Architecture diagram
```

## Key Components

### 1. Chrome Extension (MCP-B Client)

The [MCP-B Chrome Extension](https://chromewebstore.google.com/detail/mcp-b/daohopfhkdelnpemnhlekblhnikhdhfa) acts as an MCP client that discovers and routes calls to website tools.

- **Chat interface**: Natural language interaction with page tools
- **Tool inspector**: Visual listing of all registered tools
- **Multi-provider support**: Works with various LLM providers
- **No configuration required**: Install and start using

### 2. Transport Layer (@mcp-b/transports)

```bash
npm install @mcp-b/transports
```

Browser-native MCP transport implementations:

| Transport | Use Case | Communication |
|-----------|----------|---------------|
| **Tab Transport** | Same-tab communication | `window.postMessage` |
| **Extension Transport** | Extension ↔ page | Chrome extension messaging |
| **Bridge Transport** | External MCP clients | WebSocket to localhost |

```javascript
import { TabTransport } from '@mcp-b/transports';

const transport = new TabTransport();
transport.onMessage((message) => {
  // Handle incoming MCP messages
});
transport.send({ jsonrpc: "2.0", method: "tools/list" });
```

### 3. Native Server Bridge

Bridges WebMCP tools to traditional MCP server implementations running locally:

```javascript
import { NativeServerBridge } from '@mcp-b/native-server';

const bridge = new NativeServerBridge({
  command: "node",
  args: ["./my-mcp-server.js"],
  transport: "stdio"
});

// Website tools become available to native MCP servers
bridge.connect();
```

### 4. Zod Validation

Built-in Zod schema validation for tool parameters, ensuring type safety at runtime:

```javascript
import { z } from 'zod';

const searchSchema = z.object({
  query: z.string().min(1),
  category: z.enum(["electronics", "books", "clothing"]),
  maxPrice: z.number().positive().optional()
});

navigator.modelContext.registerTool({
  name: "searchProducts",
  description: "Search product catalog",
  inputSchema: searchSchema, // Zod schema auto-converted to JSON Schema
  execute: async (params) => {
    const validated = searchSchema.parse(params);
    // params are type-safe here
  }
});
```

## Quick Start

### Option 1: Script Tag (Simplest)

```html
<script src="https://unpkg.com/@mcp-b/global"></script>
<script>
  navigator.modelContext.registerTool({
    name: "greet",
    description: "Say hello to a user",
    inputSchema: {
      type: "object",
      properties: { name: { type: "string" } },
      required: ["name"]
    },
    execute: async ({ name }) => ({
      content: [{ type: "text", text: `Hello, ${name}!` }]
    })
  });
</script>
```

### Option 2: npm Package

```bash
npm install @mcp-b/global @mcp-b/transports
```

```javascript
import '@mcp-b/global';

navigator.modelContext.registerTool({
  name: "addTodo",
  description: "Add a new todo item",
  inputSchema: {
    type: "object",
    properties: {
      title: { type: "string", description: "Todo title" },
      priority: { type: "string", enum: ["low", "medium", "high"] }
    },
    required: ["title"]
  },
  execute: async ({ title, priority }) => {
    const todo = await createTodo(title, priority);
    return { content: [{ type: "text", text: JSON.stringify(todo) }] };
  }
});
```

### Option 3: React Integration

```bash
npm install @mcp-b/react-webmcp
```

See [@mcp-b/react-webmcp](../frameworks/react-webmcp.md) for React hooks documentation.

## Community Examples

| Framework | Repository | Author |
|-----------|-----------|--------|
| **Vanilla TypeScript** | [WebMCP-org/examples](https://github.com/WebMCP-org/examples) | MCP-B team |
| **React** | [WebMCP-org/examples](https://github.com/WebMCP-org/examples) | MCP-B team |
| **Vue** | [bestian/vue-MCP-B-demo](https://github.com/bestian/vue-MCP-B-demo) | Besian |
| **Nuxt 3** | [mikechao/nuxt3-mcp-b-demo](https://github.com/mikechao/nuxt3-mcp-b-demo) | Mike Chao |

## Architecture

The MCP-B architecture consists of three layers:

1. **Website Layer**: Tools registered via `navigator.modelContext` (polyfilled by `@mcp-b/global`)
2. **Transport Layer**: Communication channel between page and MCP client (`@mcp-b/transports`)
3. **Client Layer**: AI agent or extension consuming tools (Chrome extension, Claude Desktop, etc.)

## See Also

- [@mcp-b/global Polyfill](./mcp-b-global.md) — navigator.modelContext polyfill
- [@mcp-b/react-webmcp](../frameworks/react-webmcp.md) — React hooks
- [DevTools Quickstart](../official-tools/devtools-quickstart.md) — Chrome DevTools MCP integration
- [WebMCP Bridge CLI](../bridges/webmcp-bridge-cli.md) — Bridge to MCP clients
- [Alex Nahas Interview (Arcade.dev)](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview) — Origin story
- [W3C WebMCP Proposal (PR #26)](https://github.com/webmachinelearning/webmcp/pull/26) — Original W3C proposal
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Navigator model context interface](../../02-specification/navigator-model-context.md) — Navigator model context interface
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Polyfill usage guide](../../12-tutorials/polyfill-guide.md) — Polyfill usage guide
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Browser support status](../../01-overview/browser-support.md) — Browser support status
- [Community contributors](../../09-people/other-contributors.md) — Community contributors
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
