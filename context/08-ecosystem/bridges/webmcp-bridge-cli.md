# nathan-gage/webmcp-bridge

> CLI + Chrome extension bridging `navigator.modelContext` tools to any MCP client (Claude, Cursor, Windsurf) via WebSocket. Includes plugin marketplace.

## Overview

[nathan-gage/webmcp-bridge](https://github.com/nathan-gage/webmcp-bridge) provides a two-part system — a CLI server and a Chrome extension — that bridges WebMCP tools registered on any webpage to external MCP clients like Claude Desktop, Cursor, and Windsurf. The bridge communicates via local WebSocket, enabling AI coding assistants to discover and invoke web page tools without requiring page modifications.

- **Repository**: [github.com/nathan-gage/webmcp-bridge](https://github.com/nathan-gage/webmcp-bridge)
- **Author**: Nathan Gage ([@nathan-gage](https://github.com/nathan-gage))
- **License**: MIT
- **Description**: "bridge webmcp to your local agents"

## How It Works

```
┌──────────────────┐      WebSocket       ┌──────────────────┐
│  MCP Client      │ ◄──────────────────► │  Bridge CLI      │
│  (Claude Desktop,│      localhost:3456   │  Server          │
│   Cursor,        │                      │                  │
│   Windsurf,      │                      │  ┌────────────┐  │
│   VS Code)       │                      │  │ Tool Cache  │  │
└──────────────────┘                      │  └──────┬─────┘  │
                                          └─────────┼────────┘
                                                    │
                                          Chrome Extension
                                          Messages (CDP)
                                                    │
                                          ┌─────────▼────────┐
                                          │  Chrome Extension │
                                          │  (Content Script) │
                                          │                   │
                                          │  Discovers tools  │
                                          │  via navigator.   │
                                          │  modelContext      │
                                          └───────────────────┘
                                                    │
                                          ┌─────────▼────────┐
                                          │  Web Page         │
                                          │  (WebMCP tools)   │
                                          └──────────────────┘
```

1. **Chrome extension** discovers `navigator.modelContext` tools on the active page
2. **Extension** reports available tools to the **Bridge CLI** server via Chrome extension messaging
3. **Bridge CLI** exposes tools as standard MCP tools over WebSocket or stdio
4. **MCP client** (Claude, Cursor) connects and uses the tools seamlessly

## Key Features

### Seamless MCP Client Integration

Works with any MCP client that supports stdio or WebSocket transport:

- **Claude Desktop**: Full tool discovery and invocation
- **Cursor**: Use page tools while coding
- **Windsurf**: Browser tools in your IDE
- **VS Code Copilot**: Extend Copilot with web tools
- **Custom clients**: Any MCP-compatible client

### Plugin Marketplace

Inject bridge scripts on sites that don't have native WebMCP support:

```json
{
  "plugins": {
    "github.com": {
      "name": "GitHub Bridge",
      "tools": [
        {
          "name": "searchRepos",
          "description": "Search GitHub repositories",
          "script": "github-search-bridge.js"
        }
      ]
    },
    "docs.google.com": {
      "name": "Google Docs Bridge",
      "tools": [
        {
          "name": "getDocContent",
          "description": "Get document content",
          "script": "gdocs-bridge.js"
        }
      ]
    }
  }
}
```

Plugins allow the community to create WebMCP tool wrappers for popular websites that haven't adopted WebMCP natively yet.

### Auto-Discovery

- Monitors tab changes and re-discovers tools on navigation
- Detects dynamic tool registration (tools added after page load)
- Handles multiple tabs with tool isolation per tab

## Installation

### CLI Server

```bash
# Install globally
npm install -g webmcp-bridge

# Or run with npx
npx webmcp-bridge
```

### Chrome Extension

```bash
# Clone the repository
git clone https://github.com/nathan-gage/webmcp-bridge.git
cd webmcp-bridge/extension

npm install
npm run build

# Load in Chrome:
# 1. Navigate to chrome://extensions
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select extension/dist/
```

## Configuration

### MCP Client Setup

#### Claude Desktop (`~/.claude/mcp.json`)

```json
{
  "mcpServers": {
    "webmcp-bridge": {
      "command": "npx",
      "args": ["webmcp-bridge", "--port", "3456"]
    }
  }
}
```

#### Cursor

```json
{
  "mcpServers": {
    "webmcp-bridge": {
      "command": "webmcp-bridge",
      "args": ["--transport", "stdio"]
    }
  }
}
```

### Bridge CLI Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | WebSocket port | `3456` |
| `--transport` | Transport type: `websocket` or `stdio` | `websocket` |
| `--plugins-dir` | Directory for plugin scripts | `~/.webmcp-bridge/plugins` |
| `--auto-discover` | Enable auto-discovery on tab change | `true` |
| `--verbose` | Verbose logging | `false` |

## Usage Example

```bash
# 1. Start the bridge server
webmcp-bridge --port 3456

# 2. Open Chrome and navigate to a WebMCP page
# 3. The extension discovers tools and reports to the bridge

# Bridge output:
# [webmcp-bridge] Connected to Chrome extension
# [webmcp-bridge] Tab: https://flight-search.firebaseapp.com
# [webmcp-bridge] Discovered 3 tools: searchFlights, bookFlight, getFlightDetails
# [webmcp-bridge] MCP server listening on ws://localhost:3456

# 4. In Claude Desktop:
# User: "Search for flights from LAX to JFK on March 25"
# Claude: [Using searchFlights tool via webmcp-bridge]
```

## Plugin Development

Create a custom plugin for a site without native WebMCP:

```javascript
// ~/.webmcp-bridge/plugins/example-site.js
export default {
  domain: "example.com",
  tools: [
    {
      name: "getPageData",
      description: "Extract structured data from the page",
      inputSchema: {
        type: "object",
        properties: {
          selector: { type: "string", description: "CSS selector" }
        },
        required: ["selector"]
      },
      execute: async ({ selector }) => {
        const elements = document.querySelectorAll(selector);
        const data = Array.from(elements).map(el => el.textContent);
        return { content: [{ type: "text", text: JSON.stringify(data) }] };
      }
    }
  ]
};
```

## See Also

- [three-water666/WebMCP](./three-water-webmcp.md) — Universal bridge for web AI to local VS Code
- [DevTools Quickstart](../official-tools/devtools-quickstart.md) — Chrome DevTools MCP approach
- [WebMCP Adapter](./webmcp-adapter.md) — Auto-discovery adapter for frontend actions
- [AgentBoard](../extensions/agent-board.md) — Multi-provider LLM sidebar with WebMCP
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [WebMCP vs server-side MCP](../../01-overview/webmcp-vs-server-mcp.md) — WebMCP vs server-side MCP
- [MCP protocol intersection](../../02-specification/mcp-intersection.md) — MCP protocol intersection
- [MCP protocol alignment](../../07-architecture/mcp-protocol-alignment.md) — MCP protocol alignment
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Community feature requests](../../13-spec-discussions/community-external-requests.md) — Community feature requests
