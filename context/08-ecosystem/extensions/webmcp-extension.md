# Joakim-Sael/webmcp-extension

> Standalone WebMCP browser extension for discovering and interacting with `navigator.modelContext` tools.

## Overview

[Joakim-Sael/webmcp-extension](https://github.com/Joakim-Sael/webmcp-extension) is a standalone Chrome extension focused specifically on WebMCP tool discovery and interaction. Unlike multi-purpose extensions like AgentBoard, this extension provides a focused, lightweight experience for developers working with the WebMCP API.

- **Repository**: [github.com/Joakim-Sael/webmcp-extension](https://github.com/Joakim-Sael/webmcp-extension)
- **Author**: Joakim Sael ([@Joakim-Sael](https://github.com/Joakim-Sael))
- **Platform**: Chrome Extension (Manifest V3)

## Key Features

### Tool Discovery

- **Automatic scanning**: Detects all WebMCP tools on the current page
- **Imperative tools**: Discovers tools registered via `navigator.modelContext.registerTool()`
- **Declarative tools**: Detects HTML forms with `toolname` attributes
- **Real-time updates**: Refreshes tool list when new tools are registered or removed
- **Tool count badge**: Shows the number of available tools in the extension icon

### Tool Interaction

- **Parameter forms**: Auto-generated input forms based on `inputSchema`
- **Direct execution**: Execute tools with custom parameters from the extension popup
- **JSON mode**: Switch between form and raw JSON input
- **Response viewer**: Formatted display of tool execution results
- **History**: Track recent tool invocations

### Developer Features

- **Schema inspector**: View full JSON Schema definitions for each tool
- **Annotation display**: Shows `readOnlyHint`, `destructiveHint`, `idempotentHint`
- **Copy schema**: Copy tool schemas to clipboard for documentation or testing
- **Export tools**: Export all tool definitions as JSON for sharing

## Installation

### From Source

```bash
git clone https://github.com/Joakim-Sael/webmcp-extension.git
cd webmcp-extension

npm install
npm run build

# Load in Chrome:
# 1. Navigate to chrome://extensions
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the build output directory
```

## Usage

### Discovering Tools

1. Enable WebMCP in Chrome (`chrome://flags` → "WebMCP for testing")
2. Navigate to any WebMCP-enabled page
3. Click the extension icon
4. View all registered tools with their descriptions and schemas

### Executing a Tool

1. Click on a tool in the list to expand it
2. Fill in the parameter form or switch to JSON mode
3. Click **"Execute"**
4. View the result in the response panel

### Exporting Tool Definitions

1. Click the **"Export"** button in the extension header
2. All tool definitions are copied as JSON
3. Use the export for documentation, testing, or sharing with teammates

## Extension Structure

```
webmcp-extension/
├── src/
│   ├── popup/           # Extension popup UI
│   │   ├── popup.html
│   │   ├── popup.js
│   │   └── popup.css
│   ├── content/         # Content script for tool discovery
│   │   └── content.js
│   ├── background/      # Service worker
│   │   └── background.js
│   └── utils/           # Shared utilities
│       ├── schema.js    # JSON Schema to form conversion
│       └── executor.js  # Tool execution logic
├── manifest.json        # Manifest V3 configuration
├── icons/
└── package.json
```

## Comparison with Other Extensions

| Feature | webmcp-extension | Model Context Tool Inspector | AgentBoard |
|---------|-----------------|------------------------------|-----------|
| Focus | WebMCP tools only | WebMCP tools + Gemini | Multi-LLM + WebMCP |
| LLM integration | ❌ | ✅ (Gemini) | ✅ (4 providers) |
| Tool discovery | ✅ | ✅ | ✅ |
| Tool execution | ✅ | ✅ | ✅ |
| Remote MCP servers | ❌ | ❌ | ✅ |
| Export tools | ✅ | ❌ | ❌ |
| Lightweight | ✅ | ✅ | ❌ |
| Complexity | Low | Medium | High |

## Requirements

- Chrome 146+ (Canary) with WebMCP flag enabled
- No API keys or external services required

## See Also

- [AgentBoard](./agent-board.md) — Feature-rich AI switchboard extension
- [Agentic Web Learning Tool](./agentic-web-learning-tool.md) — Agentic workflow extension
- [Model Context Tool Inspector](../official-tools/model-context-tool-inspector.md) — Official Chrome inspector
- [WebMCP Bridge CLI](../bridges/webmcp-bridge-cli.md) — Bridge to external MCP clients
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Extension API architecture](../../07-architecture/extension-api.md) — Extension API architecture
- [Browser agent model](../../07-architecture/browser-agent-model.md) — Browser agent model
- [Chrome setup guide](../../12-tutorials/chrome-setup.md) — Chrome setup guide
- [WebExtensions discussion](../../13-spec-discussions/webextensions-external-agents.md) — WebExtensions discussion
- [Threat model overview](../../04-security/threat-model-overview.md) — Threat model overview
- [Google Chrome's role](../../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
