# ripulio/web-mcp

> Monorepo implementing WebMCP with MCP server CLI, Chrome extension, DevTools panel, and `navigator.modelContext` polyfill.

## Overview

[ripulio/web-mcp](https://github.com/ripulio/web-mcp) is an independent implementation of the WebMCP browser proposal. It provides a full-stack monorepo with multiple packages: an MCP server CLI for exposing page tools to external clients, a Chrome extension for in-browser interaction, a DevTools panel for debugging, and a `navigator.modelContext` polyfill for cross-browser compatibility.

- **Repository**: [github.com/ripulio/web-mcp](https://github.com/ripulio/web-mcp)
- **Stars**: ~2 (as of Feb 2026)
- **License**: MIT
- **Releases**: 8 (as of Feb 2026)
- **Latest**: @ripulio/web-mcp-extension@0.2.3

## Key Components

### 1. MCP Server CLI

A command-line MCP server that connects to a browser tab and exposes its WebMCP tools to any MCP client:

```bash
# Install the CLI
npm install -g @ripulio/web-mcp-server

# Start the MCP server, connecting to a running Chrome instance
web-mcp-server --port 9222
```

The CLI:
- Connects to Chrome via CDP (Chrome DevTools Protocol)
- Discovers all `navigator.modelContext` tools on the active page
- Exposes them as standard MCP tools over stdio or WebSocket
- Supports tool invocation forwarding from external clients

### 2. Chrome Extension

A Chrome extension providing an interface for interacting with WebMCP tools:

- **Tool discovery**: Lists all registered tools on the current page
- **Manual execution**: Execute tools with custom parameters
- **Real-time updates**: Detects new tool registrations dynamically
- **Minimal UI**: Clean, developer-focused interface

### 3. DevTools Panel

A Chrome DevTools panel integrated into the browser's developer tools:

- **Dedicated tab**: Appears as a new panel in Chrome DevTools
- **Tool tree view**: Hierarchical display of all registered tools
- **Schema inspector**: Expand tools to see full `inputSchema` details
- **Execution console**: Test tools directly from DevTools
- **Event log**: Monitor `toolactivated` and `toolcancel` events

### 4. Polyfill

Implements `navigator.modelContext` for browsers without native support:

```javascript
import '@ripulio/webmcp-polyfill';

// navigator.modelContext is now available
navigator.modelContext.registerTool({
  name: "myTool",
  description: "A tool that does something useful",
  inputSchema: {
    type: "object",
    properties: {
      input: { type: "string" }
    }
  },
  execute: async ({ input }) => ({
    content: [{ type: "text", text: `Processed: ${input}` }]
  })
});
```

## Monorepo Structure

```
web-mcp/
├── packages/
│   ├── web-mcp-server/       # MCP server CLI
│   │   ├── src/
│   │   └── package.json     # @ripulio/web-mcp-server
│   ├── web-mcp-extension/   # Chrome extension
│   │   ├── src/
│   │   ├── manifest.json
│   │   └── package.json     # @ripulio/web-mcp-extension
│   ├── web-mcp-devtools/    # DevTools panel
│   │   ├── src/
│   │   └── package.json
│   └── polyfill/            # navigator.modelContext polyfill
│       ├── src/
│       └── package.json     # @ripulio/webmcp-polyfill
├── claude-plugins/          # Pre-built tool plugins
├── package.json
├── package-lock.json
└── README.md
```

## Installation

### Full Monorepo

```bash
git clone https://github.com/ripulio/web-mcp.git
cd web-mcp
npm install
npm run build
```

### Individual Packages

```bash
# MCP Server CLI
npm install -g @ripulio/web-mcp-server

# Polyfill
npm install @ripulio/webmcp-polyfill

# Extension (load from source)
cd packages/web-mcp-extension
npm run build
# Load unpacked in chrome://extensions
```

## Usage with MCP Clients

### Claude Desktop

```json
{
  "mcpServers": {
    "web-mcp": {
      "command": "npx",
      "args": ["@ripulio/web-mcp-server", "--port", "9222"]
    }
  }
}
```

### Cursor

```json
{
  "mcpServers": {
    "web-mcp": {
      "command": "web-mcp-server",
      "args": ["--port", "9222"]
    }
  }
}
```

## Comparison with Other Implementations

| Feature | ripulio/web-mcp | MiguelsPizza/WebMCP | @mcp-b/global |
|---------|----------------|---------------------|---------------|
| MCP Server CLI | ✅ | ❌ | ❌ |
| Chrome Extension | ✅ | ✅ | ❌ |
| DevTools Panel | ✅ | ❌ | ❌ |
| Polyfill | ✅ | ✅ | ✅ |
| Zod validation | ❌ | ✅ | ❌ |
| Transport layer | CDP-based | Multi-transport | N/A |
| Community size | Small | Large (~1000 stars) | Part of MCP-B |

## Requirements

- Chrome 146+ (Canary) for native WebMCP support (or use the polyfill)
- Node.js 18+ for the MCP server CLI
- npm for monorepo development

## See Also

- [@mcp-b/global](./mcp-b-global.md) — Alternative polyfill from MCP-B project
- [MiguelsPizza/WebMCP](./miguels-pizza-webmcp.md) — MCP-B reference implementation
- [opentiny/next-sdk](./opentiny-next-sdk.md) — Frontend AI SDK with WebMCP
- [DevTools Quickstart](../official-tools/devtools-quickstart.md) — Chrome DevTools MCP integration
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Navigator model context interface](../../02-specification/navigator-model-context.md) — Navigator model context interface
- [Polyfill usage guide](../../12-tutorials/polyfill-guide.md) — Polyfill usage guide
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Browser support status](../../01-overview/browser-support.md) — Browser support status
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [API naming discussions](../../13-spec-discussions/api-surface-naming.md) — API naming discussions
