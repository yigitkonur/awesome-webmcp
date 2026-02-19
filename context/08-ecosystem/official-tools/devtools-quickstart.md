# WebMCP-org/chrome-devtools-quickstart

> Quickstart for connecting Chrome DevTools MCP to WebMCP tools, with ~89% token reduction vs. screenshot automation.

## Overview

[WebMCP-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart) provides a quickstart guide and reference configuration for connecting the Chrome DevTools MCP server to WebMCP tools on a page. It demonstrates how AI agents using MCP clients (Claude Desktop, Cursor, VS Code Copilot) can interact with WebMCP-enabled websites through Chrome DevTools Protocol (CDP) rather than screenshot-based automation.

- **Repository**: [github.com/WebMCP-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart)
- **Related**: [Chrome DevTools MCP blog post](https://developer.chrome.com/blog/chrome-devtools-mcp)
- **npm package**: [@mcp-b/chrome-devtools-mcp](https://www.npmjs.com/package/@mcp-b/chrome-devtools-mcp)

## The ~89% Token Reduction Benchmark

The repository includes a benchmark comparing two approaches for AI agent interaction with websites:

| Approach | Tokens per Interaction | Reliability |
|----------|----------------------|-------------|
| **Screenshot automation** | ~10,000+ tokens (image encoding + parsing) | Fragile, layout-dependent |
| **WebMCP via DevTools MCP** | ~1,100 tokens (structured tool calls) | Reliable, schema-validated |

**Result**: Using WebMCP tools through Chrome DevTools MCP reduces token usage by approximately **89%** compared to screenshot-based automation while significantly improving reliability and speed.

### Why the Reduction?

- **No screenshots**: AI agents call structured tools instead of analyzing images
- **No DOM parsing**: Tools define explicit schemas instead of requiring HTML analysis
- **No pixel coordinates**: Actions are semantic (tool names + parameters) not spatial
- **Smaller payloads**: JSON tool responses vs. base64-encoded screenshots

## How It Works

```
┌─────────────┐     MCP Protocol     ┌──────────────────┐
│  MCP Client  │ ◄──────────────────► │  Chrome DevTools  │
│  (Claude,    │                      │  MCP Server       │
│   Cursor,    │     WebSocket/CDP    │                   │
│   Copilot)   │                      │  ┌──────────────┐ │
└─────────────┘                      │  │ WebMCP Tools  │ │
                                     │  │ on the page   │ │
                                     │  └──────────────┘ │
                                     └──────────────────┘
```

1. **Website registers tools** via `navigator.modelContext.registerTool()`
2. **Chrome DevTools MCP server** discovers tools through CDP
3. **MCP client** (Claude, Cursor) connects to the DevTools MCP server
4. **AI agent** discovers available tools and invokes them via structured calls
5. **Tool responses** flow back through the same channel

## Setup

### Prerequisites

- Chrome 146+ (Canary) with WebMCP flag enabled
- Node.js 18+
- An MCP client (Claude Desktop, Cursor, VS Code Copilot, etc.)

### Step 1: Install Chrome DevTools MCP

```bash
npm install -g @mcp-b/chrome-devtools-mcp
```

### Step 2: Launch Chrome with Remote Debugging

```bash
# macOS
/Applications/Google\ Chrome\ Canary.app/Contents/MacOS/Google\ Chrome\ Canary \
  --remote-debugging-port=9222 \
  --user-data-dir=/tmp/chrome-webmcp

# Linux
google-chrome-canary --remote-debugging-port=9222

# Windows
"C:\Users\%USERNAME%\AppData\Local\Google\Chrome SxS\Application\chrome.exe" ^
  --remote-debugging-port=9222
```

### Step 3: Enable WebMCP Flag

1. Navigate to `chrome://flags`
2. Search for "WebMCP"
3. Set **"WebMCP for testing"** to **Enabled**
4. Relaunch Chrome

### Step 4: Navigate to a WebMCP-Enabled Page

Open any page with registered WebMCP tools (e.g., [flight-search.firebaseapp.com](https://flight-search.firebaseapp.com)).

### Step 5: Connect Your MCP Client

#### Claude Desktop Configuration

Add to your Claude Desktop MCP config (`~/.claude/mcp.json`):

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["@mcp-b/chrome-devtools-mcp", "--port", "9222"]
    }
  }
}
```

#### Cursor Configuration

Add to your Cursor MCP settings:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["@mcp-b/chrome-devtools-mcp", "--port", "9222"]
    }
  }
}
```

### Step 6: Use WebMCP Tools from Your AI Agent

Once connected, the AI agent can discover and invoke all WebMCP tools on the active page:

```
User: "Find me flights from London to Tokyo on March 20th"

Agent: I'll use the searchFlights tool on the page...
       [Calling searchFlights with {origin: "LHR", destination: "NRT", date: "2026-03-20"}]
       
       Found 3 flights:
       1. BA005 - Departs 10:30, Arrives 06:30+1 - $1,240
       2. JL402 - Departs 19:00, Arrives 15:00+1 - $980
       3. NH211 - Departs 11:45, Arrives 07:45+1 - $1,100
```

## Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | Chrome remote debugging port | `9222` |
| `--host` | Chrome remote debugging host | `localhost` |
| `--tab` | Target tab index or URL pattern | Active tab |
| `--timeout` | Connection timeout (ms) | `30000` |

## Comparison with Other Approaches

| Feature | DevTools MCP + WebMCP | Screenshot Automation | DOM Scraping |
|---------|----------------------|----------------------|--------------|
| Token usage | ~1,100/interaction | ~10,000/interaction | ~3,000/interaction |
| Reliability | High (schema-validated) | Low (layout-dependent) | Medium (DOM-dependent) |
| Speed | Fast (direct calls) | Slow (render + encode) | Medium |
| Setup complexity | Medium | Low | Low |
| Requires page support | Yes (WebMCP tools) | No | No |

## Troubleshooting

### Common Issues

1. **"No WebMCP tools found"**: Ensure the WebMCP flag is enabled and the page has registered tools
2. **Connection refused**: Check that Chrome was launched with `--remote-debugging-port=9222`
3. **Tool execution timeout**: Increase the `--timeout` value or check tool handler performance
4. **Multiple tabs**: Use `--tab` to target a specific tab by URL pattern

## See Also

- [Chrome Labs Toolkit](./chrome-labs-toolkit.md) — Official toolkit overview
- [Model Context Tool Inspector](./model-context-tool-inspector.md) — Visual tool inspection
- [WebMCP Bridge CLI](../bridges/webmcp-bridge-cli.md) — Alternative bridge via WebSocket
- [Chrome DevTools MCP Blog](https://developer.chrome.com/blog/chrome-devtools-mcp) — Official Google blog post
- [MCP-B Chrome DevTools npm](https://www.npmjs.com/package/@mcp-b/chrome-devtools-mcp) — npm package
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Navigator model context API](../../02-specification/navigator-model-context.md) — Navigator model context API
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Chrome flag setup guide](../../12-tutorials/chrome-setup.md) — Chrome flag setup guide
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Google Chrome's role in WebMCP](../../14-industry/google-chrome-involvement.md) — Google Chrome's role in WebMCP
- [Browser support status](../../01-overview/browser-support.md) — Browser support status
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
