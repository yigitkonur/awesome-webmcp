# Model Context Tool Inspector

> Official Chrome extension for inspecting, testing, and debugging WebMCP tools registered on any webpage.

## Overview

The **Model Context Tool Inspector** is an official Chrome extension published by the Google Chrome team as part of the [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) repository. It provides a visual interface for discovering all WebMCP tools registered on a page, manually executing them with custom parameters, and testing them against Google's Gemini API.

- **Chrome Web Store**: [Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
- **Extension ID**: `gbpdfapgefenggkahomfgkhfehlcenpd`
- **Source Code**: Part of [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools)
- **License**: Apache-2.0

## Key Features

### Tool Discovery

- **Automatic detection** of all tools registered via the imperative `navigator.modelContext.registerTool()` API
- **Declarative tool scanning** for HTML forms with `toolname` and `tooldescription` attributes
- **Real-time updates** when tools are added, removed, or modified
- **Context detection** for `navigator.modelContext.provideContext()` calls

### Tool Inspection

- **Schema viewer**: View the full JSON Schema (`inputSchema`) for each tool's parameters
- **Annotation display**: Shows tool annotations such as `readOnlyHint`, `destructiveHint`, and `idempotentHint`
- **Description analysis**: Displays tool names and descriptions as they would appear to AI agents
- **Output schema preview**: Shows `outputSchema` when defined (spec issue [#9](https://github.com/webmachinelearning/webmcp/issues/9))

### Manual Execution

- **Parameter form**: Auto-generated form based on the tool's `inputSchema`
- **JSON input mode**: Raw JSON parameter entry for advanced testing
- **Response viewer**: Displays tool execution results including content type and structure
- **Error handling**: Shows error messages and stack traces when tool execution fails
- **Execution history**: Tracks recent tool invocations and their results

### Gemini API Testing

- **Built-in Gemini integration**: Test how Google's Gemini model interacts with your registered tools
- **Conversation mode**: Send natural language queries and observe how Gemini selects and invokes tools
- **Multi-turn testing**: Maintain conversation context across multiple tool invocations
- **API key management**: Securely store your Gemini API key in the extension

## Installation

### From Chrome Web Store

1. Open Chrome 146+ (Canary) with WebMCP enabled
2. Visit the [Chrome Web Store listing](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
3. Click **"Add to Chrome"**
4. Pin the extension for easy access

### From Source

```bash
# Clone the repository
git clone https://github.com/GoogleChromeLabs/webmcp-tools.git
cd webmcp-tools/extension

# Install dependencies
npm install

# Build the extension
npm run build

# Load in Chrome:
# 1. Navigate to chrome://extensions
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the build output directory
```

### Enable WebMCP in Chrome

Before the extension can detect tools, WebMCP must be enabled:

1. Open `chrome://flags`
2. Search for "WebMCP"
3. Set **"WebMCP for testing"** to **Enabled**
4. Relaunch Chrome

## Usage

### Inspecting Tools on a Page

1. Navigate to a page with WebMCP tools (e.g., [flight-search.firebaseapp.com](https://flight-search.firebaseapp.com))
2. Click the Model Context Tool Inspector icon in the toolbar
3. The popup displays all registered tools with:
   - Tool name
   - Description
   - Input schema summary
   - Annotations (read-only, destructive, etc.)

### Testing a Tool Manually

1. Click on a tool in the list to expand it
2. Fill in the parameter form (auto-generated from `inputSchema`)
3. Click **"Execute"**
4. View the response in the results panel

### Testing with Gemini

1. Click the **"Gemini"** tab in the extension
2. Enter your Gemini API key (stored locally)
3. Type a natural language query (e.g., "Find flights from London to New York on March 15")
4. Observe how Gemini selects the appropriate tool and constructs parameters
5. View the tool execution result and Gemini's response

## Interface Overview

```
┌─────────────────────────────────────────┐
│  Model Context Tool Inspector           │
├─────────────────────────────────────────┤
│  Tools (3 registered)                   │
│                                         │
│  ▼ searchFlights                        │
│    Description: Search for available... │
│    Schema: { origin, destination, date }│
│    Annotations: readOnlyHint: true      │
│    [Execute] [View Schema]              │
│                                         │
│  ▶ bookFlight                           │
│  ▶ getFlightDetails                     │
│                                         │
├─────────────────────────────────────────┤
│  [Tools] [Gemini] [History] [Settings]  │
└─────────────────────────────────────────┘
```

## Supported Tool Registration Methods

| Method | Detected | Details |
|--------|----------|---------|
| `navigator.modelContext.registerTool()` | ✅ | Full schema, annotations, execute function |
| `navigator.modelContext.provideContext()` | ✅ | Batch tool registration |
| `<form toolname="...">` | ✅ | Declarative HTML tools |
| `<input toolparamdescription="...">` | ✅ | Parameter descriptions in forms |
| `toolautosubmit` attribute | ✅ | Auto-submit flag detection |

## Requirements

- **Chrome 146+** (Canary) with WebMCP flag enabled
- **Gemini API key** (optional, for AI testing features)
- No additional dependencies required

## Limitations

- Only detects tools on the active tab
- Gemini API testing requires a valid API key and internet connection
- Cannot modify tool registrations (read-only inspector)
- Cross-origin iframe tools may not be visible (per spec [#57](https://github.com/webmachinelearning/webmcp/issues/57))

## See Also

- [Chrome Labs Toolkit](./chrome-labs-toolkit.md) — Parent project overview
- [WebMCP Evals CLI](./webmcp-evals-cli.md) — Automated tool quality evaluation
- [DevTools Quickstart](./devtools-quickstart.md) — Chrome DevTools MCP integration
- [AgentBoard Extension](../extensions/agent-board.md) — Multi-provider LLM sidebar with WebMCP
- [Chrome Early Preview](https://developer.chrome.com/blog/webmcp-epp) — Official setup guide
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Input schema specification](../../02-specification/input-schema.md) — Input schema specification
- [Tool annotations](../../02-specification/annotations.md) — Tool annotations
- [Getting started with WebMCP](../../12-tutorials/getting-started.md) — Getting started with WebMCP
- [Testing tools and workflows](../../12-tutorials/testing-tools.md) — Testing tools and workflows
- [Chrome setup guide](../../12-tutorials/chrome-setup.md) — Chrome setup guide
- [Google Chrome's role in WebMCP](../../14-industry/google-chrome-involvement.md) — Google Chrome's role in WebMCP
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
