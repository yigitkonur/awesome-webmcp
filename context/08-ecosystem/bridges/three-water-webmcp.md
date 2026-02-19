# three-water666/WebMCP

> Universal bridge connecting web AI (Gemini, ChatGPT, DeepSeek) to local VS Code via MCP, with auto port discovery.

## Overview

[three-water666/WebMCP](https://github.com/three-water666/WebMCP) is a universal bridge tool that connects web-based AI assistants (Google Gemini, ChatGPT, DeepSeek, and others) to a local VS Code development environment via MCP. It enables cloud AI models accessed through a browser to use local development tools — file editing, terminal commands, project navigation — without requiring API keys or local model setup.

- **Repository**: [github.com/three-water666/WebMCP](https://github.com/three-water666/WebMCP)
- **Author**: [@three-water666](https://github.com/three-water666)
- **License**: MIT

## The Problem It Solves

Cloud AI assistants (Gemini, ChatGPT) run in the browser and cannot access your local filesystem or development tools. Developers who prefer cloud AI models still need to manually copy-paste code between the browser and their IDE.

three-water666/WebMCP bridges this gap by:
1. Running a local MCP server connected to VS Code
2. Providing a browser extension that connects web AI to the local server
3. Enabling cloud AI to directly edit files, run commands, and navigate projects

## Components

### 1. VS Code Extension

A VS Code extension that exposes development tools as MCP endpoints:

| Tool | Description |
|------|-------------|
| `vscode.openFile` | Open a file in the editor |
| `vscode.editFile` | Make edits to a file |
| `vscode.runTerminal` | Execute a terminal command |
| `vscode.searchFiles` | Search files by name or content |
| `vscode.getFileTree` | Get project directory structure |
| `vscode.getDiagnostics` | Get linting/type errors |
| `vscode.getSelection` | Get current editor selection |
| `vscode.insertSnippet` | Insert a code snippet |

### 2. Browser Extension

A Chrome extension that integrates with web AI interfaces:

- **Gemini**: Injects MCP tool support into the Gemini chat interface
- **ChatGPT**: Adds local development tool access to ChatGPT
- **DeepSeek**: Connects DeepSeek's web interface to local tools
- **Custom**: Extensible to other web AI interfaces

### 3. Electron App

An optional desktop application providing:

- **Auto port discovery**: Automatically finds and connects to the VS Code extension
- **Connection management**: Visual UI for managing browser-to-IDE connections
- **Status monitoring**: Real-time view of active connections and tool invocations
- **Configuration**: Settings for ports, allowed tools, and security preferences

## Architecture

```
┌──────────────────────┐
│  Web AI              │
│  (Gemini / ChatGPT / │
│   DeepSeek)          │
└──────────┬───────────┘
           │ In-page injection
┌──────────▼───────────┐
│  Browser Extension   │
│  - AI interface hook │
│  - Tool discovery    │
│  - Message routing   │
└──────────┬───────────┘
           │ WebSocket (localhost)
┌──────────▼───────────┐
│  Electron App        │
│  (Optional bridge)   │
│  - Auto port finder  │
│  - Connection mgmt   │
└──────────┬───────────┘
           │ MCP Protocol
┌──────────▼───────────┐
│  VS Code Extension   │
│  - File operations   │
│  - Terminal access    │
│  - Diagnostics       │
│  - Project structure │
└──────────────────────┘
```

## Auto Port Discovery

The Electron app automatically discovers VS Code MCP server instances:

```javascript
// Auto-discovers VS Code extension port
const discovery = new PortDiscovery({
  startPort: 9500,
  endPort: 9600,
  protocol: "mcp",
  timeout: 5000
});

const vsCodePort = await discovery.find();
// Found VS Code MCP server at localhost:9527
```

## Installation

### VS Code Extension

```bash
# Install from VS Code marketplace (when published)
code --install-extension three-water666.webmcp

# Or from source
git clone https://github.com/three-water666/WebMCP.git
cd WebMCP/vscode-extension
npm install
npm run build
# Install the .vsix file in VS Code
```

### Browser Extension

```bash
cd WebMCP/browser-extension
npm install
npm run build
# Load unpacked in chrome://extensions
```

### Electron App (Optional)

```bash
cd WebMCP/electron-app
npm install
npm start
```

## Usage

### Basic Workflow

1. **Start VS Code** with the extension installed
2. **Open your project** in VS Code
3. **Navigate to Gemini/ChatGPT** in Chrome with the browser extension installed
4. **The Electron app** (if running) auto-discovers the VS Code connection
5. **Ask the AI** to edit code, and it operates directly on your local files

### Example Interaction

```
User (in Gemini): "Fix the TypeScript error in src/utils/parser.ts"

Gemini:
1. [vscode.getDiagnostics] Getting errors in src/utils/parser.ts
   → Found: Type 'string' is not assignable to type 'number' at line 42

2. [vscode.openFile] Opening src/utils/parser.ts
   → File content loaded

3. [vscode.editFile] Fixing line 42: changing parseInt(value) to Number(value)
   → Edit applied

4. [vscode.getDiagnostics] Verifying fix
   → No errors found

Done! Fixed the type error by using Number() instead of parseInt().
```

## Security Considerations

- **Localhost only**: Communication is restricted to `localhost`
- **Allowlisted tools**: Configure which tools are available
- **Confirmation prompts**: Optional confirmation for destructive operations
- **Read-only mode**: Option to restrict to read-only tools
- **Session-based**: Connections expire when browser/VS Code closes

## Comparison with Other Bridges

| Feature | three-water666/WebMCP | webmcp-bridge | DevTools MCP |
|---------|----------------------|---------------|--------------|
| Direction | Browser AI → Local IDE | Page tools → MCP client | Page tools → MCP client |
| Web AI support | ✅ (Gemini, ChatGPT, etc.) | ❌ | ❌ |
| VS Code integration | ✅ (extension) | ❌ | ❌ |
| WebMCP tool bridging | ❌ | ✅ | ✅ |
| Auto port discovery | ✅ (Electron) | ❌ | ❌ |
| Desktop app | ✅ (Electron) | ❌ | ❌ |

## See Also

- [WebMCP Bridge CLI](./webmcp-bridge-cli.md) — Bridge page tools to MCP clients
- [WebMCP Adapter](./webmcp-adapter.md) — Auto-discovery adapter
- [DevTools Quickstart](../official-tools/devtools-quickstart.md) — Chrome DevTools MCP
- [AgentBoard](../extensions/agent-board.md) — Multi-provider LLM sidebar
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [WebMCP vs server-side MCP](../../01-overview/webmcp-vs-server-mcp.md) — WebMCP vs server-side MCP
- [MCP protocol intersection](../../02-specification/mcp-intersection.md) — MCP protocol intersection
- [MCP protocol alignment](../../07-architecture/mcp-protocol-alignment.md) — MCP protocol alignment
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Community contributors](../../09-people/other-contributors.md) — Community contributors
- [Enterprise workflow use case](../../11-use-cases/enterprise-workflows.md) — Enterprise workflow use case
