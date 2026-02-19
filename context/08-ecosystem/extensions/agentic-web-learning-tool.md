# amedina/agentic-web-learning-tool

> Chrome extension framework for agentic AI workflows with visual composition, MCP server integration, and Chrome built-in AI.

## Overview

[amedina/agentic-web-learning-tool](https://github.com/amedina/agentic-web-learning-tool) is a Chrome extension framework designed for building agentic AI workflows in the browser. It combines visual workflow composition, MCP server integration, and Chrome's built-in AI capabilities (Gemini Nano) to create interactive learning and automation experiences.

- **Repository**: [github.com/amedina/agentic-web-learning-tool](https://github.com/amedina/agentic-web-learning-tool)
- **Author**: [@amedina](https://github.com/amedina)
- **License**: MIT
- **Platform**: Chrome Extension (Manifest V3)

## Key Features

### Visual Workflow Composition

Build complex AI workflows using a visual drag-and-drop interface:

- **Node-based editor**: Connect input, processing, and output nodes
- **Workflow templates**: Pre-built templates for common learning scenarios
- **Conditional branching**: Route workflows based on AI analysis results
- **Loop support**: Iterate over collections or repeat until conditions are met
- **State management**: Persist workflow state across sessions

### MCP Server Integration

Connect to external MCP servers for extended capabilities:

```json
{
  "mcpServers": {
    "local-tools": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/path/to/docs"]
    },
    "web-search": {
      "url": "ws://localhost:3001/mcp"
    }
  }
}
```

### WebMCP Tool Discovery

Automatically discovers and integrates WebMCP tools from the current page:

- Detects `navigator.modelContext` tool registrations
- Detects declarative `toolname` HTML attributes
- Incorporates discovered tools into workflow nodes
- Enables AI agents to use page tools as part of learning workflows

### Chrome Built-in AI Playground

Interactive playground for testing Chrome's built-in AI capabilities:

- **Gemini Nano integration**: Use on-device AI for privacy-sensitive tasks
- **Prompt engineering UI**: Test and refine prompts
- **Side-by-side comparison**: Compare built-in AI vs. cloud model responses
- **Token counter**: Track prompt and response token usage

## Architecture

```
┌──────────────────────────────────────┐
│  Chrome Extension                    │
│                                      │
│  ┌──────────────┐  ┌──────────────┐  │
│  │  Workflow     │  │  AI          │  │
│  │  Composer     │  │  Playground  │  │
│  │  (Visual)     │  │  (Gemini)    │  │
│  └──────┬───────┘  └──────┬───────┘  │
│         │                 │          │
│  ┌──────▼─────────────────▼───────┐  │
│  │  Agent Runtime                 │  │
│  │  - MCP Client                  │  │
│  │  - WebMCP Discovery            │  │
│  │  - Chrome AI API               │  │
│  └──────┬─────────────────┬───────┘  │
│         │                 │          │
└─────────┼─────────────────┼──────────┘
          │                 │
   ┌──────▼──────┐  ┌──────▼──────┐
   │  MCP Servers │  │  Page Tools │
   │  (External)  │  │  (WebMCP)   │
   └─────────────┘  └─────────────┘
```

## Workflow Example: Research Assistant

```yaml
# Workflow: Research a topic using page tools and external sources
nodes:
  - id: input
    type: user-input
    prompt: "What topic would you like to research?"

  - id: page-search
    type: webmcp-tool
    tool: searchProducts  # WebMCP tool from the page
    params:
      query: "{{input.value}}"

  - id: summarize
    type: ai-process
    model: gemini-nano
    prompt: "Summarize these results: {{page-search.output}}"

  - id: output
    type: display
    content: "{{summarize.output}}"

connections:
  - from: input → to: page-search
  - from: page-search → to: summarize
  - from: summarize → to: output
```

## Installation

### From Source

```bash
git clone https://github.com/amedina/agentic-web-learning-tool.git
cd agentic-web-learning-tool

npm install
npm run build

# Load in Chrome:
# 1. Navigate to chrome://extensions
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the dist/ directory
```

## Use Cases

| Use Case | Description |
|----------|-------------|
| **Interactive Learning** | Build AI-powered learning experiences that leverage page content |
| **Research Workflows** | Chain WebMCP tools with AI analysis for automated research |
| **Content Analysis** | Extract, summarize, and organize information from web pages |
| **Form Automation** | Intelligent form filling using WebMCP declarative tools |
| **Accessibility Testing** | Use AI to evaluate and improve page accessibility |

## Requirements

- Chrome 146+ (for WebMCP and built-in AI support)
- Node.js 18+ (for building from source)
- Chrome AI features enabled (`chrome://flags` → "Built-in AI" features)

## See Also

- [AgentBoard](./agent-board.md) — Multi-provider LLM sidebar with WebMCP
- [WebMCP Extension](./webmcp-extension.md) — Standalone WebMCP extension
- [Model Context Tool Inspector](../official-tools/model-context-tool-inspector.md) — Official tool inspector
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Chrome Built-in AI](https://developer.chrome.com/docs/ai/built-in) — Chrome AI documentation
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Extension API architecture](../../07-architecture/extension-api.md) — Extension API architecture
- [Human-in-the-loop patterns](../../06-user-interaction/human-in-the-loop.md) — Human-in-the-loop patterns
- [Accessibility use case](../../11-use-cases/accessibility.md) — Accessibility use case
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [WebExtensions discussion](../../13-spec-discussions/webextensions-external-agents.md) — WebExtensions discussion
- [Accessibility discussions](../../13-spec-discussions/accessibility-broader-web.md) — Accessibility discussions
- [Threat model overview](../../04-security/threat-model-overview.md) — Threat model overview
- [Agentic web landscape](../../14-industry/agentic-web-landscape.md) — Agentic web landscape
