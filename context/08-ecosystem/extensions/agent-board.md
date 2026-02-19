# igrigorik/AgentBoard

> AI switchboard Chrome extension by Ilya Grigorik (Shopify) — multi-provider LLM sidebar, scriptable WebMCP tools, remote MCP server support.

## Overview

[igrigorik/AgentBoard](https://github.com/igrigorik/AgentBoard) is a Chrome extension created by **Ilya Grigorik** ([@igrigorik](https://github.com/igrigorik)), a well-known web performance expert and Director of Engineering at Shopify. AgentBoard acts as an "AI switchboard" in the browser, providing a sidebar for interacting with multiple LLM providers while leveraging WebMCP tools registered on the current page.

- **Repository**: [github.com/igrigorik/AgentBoard](https://github.com/igrigorik/AgentBoard)
- **Chrome Web Store**: [AgentBoard](https://chromewebstore.google.com/detail/agentboard/jlmajjfiibgnejlndfoboojahlclgoam)
- **Stars**: ~107 (as of Feb 2026)
- **Forks**: ~18
- **License**: MIT
- **Author**: Ilya Grigorik (Shopify)

## Key Features

### Multi-Provider LLM Sidebar

AgentBoard provides a sidebar panel that supports multiple AI model providers:

| Provider | Models | Auth |
|----------|--------|------|
| **OpenAI** | GPT-4, GPT-4o, GPT-3.5-turbo | API key |
| **Anthropic** | Claude 3 Opus, Sonnet, Haiku | API key |
| **Google** | Gemini Pro, Gemini Ultra | API key |
| **Ollama** | Any local model | Local (no key) |

- **Provider switching**: Switch between LLM providers mid-conversation
- **Model selection**: Choose specific models per provider
- **Local models**: Full support for Ollama (runs on localhost)
- **API key management**: Securely stored in extension storage

### Scriptable WebMCP Tools

AgentBoard can discover and use WebMCP tools running in the page context:

```javascript
// Tools registered on the page are automatically available
navigator.modelContext.registerTool({
  name: "getProductDetails",
  description: "Get details for a product on this page",
  inputSchema: {
    type: "object",
    properties: {
      productId: { type: "string" }
    },
    required: ["productId"]
  },
  execute: async ({ productId }) => {
    // AgentBoard invokes this in the page context
    const product = await fetchProduct(productId);
    return { content: [{ type: "text", text: JSON.stringify(product) }] };
  }
});
```

AgentBoard also supports **custom scriptable tools** defined in the extension:

```javascript
// Define tools directly in AgentBoard's settings
{
  "tools": [
    {
      "name": "getCurrentPageInfo",
      "description": "Get information about the current page",
      "script": "return { title: document.title, url: location.href }"
    }
  ]
}
```

### Remote MCP Server Support

Connect to external MCP servers alongside in-page WebMCP tools:

```json
{
  "mcpServers": [
    {
      "name": "my-backend",
      "url": "ws://localhost:3001/mcp",
      "transport": "websocket"
    },
    {
      "name": "cloud-tools",
      "url": "https://api.example.com/mcp/sse",
      "transport": "sse"
    }
  ]
}
```

### Command Templates

Pre-defined command templates for common tasks:

```
/summarize    - Summarize the current page
/extract      - Extract structured data from the page
/translate    - Translate page content
/analyze      - Analyze page for SEO/accessibility
/custom       - Run a custom command template
```

## Installation

### From Chrome Web Store

1. Visit the [AgentBoard Chrome Web Store listing](https://chromewebstore.google.com/detail/agentboard/jlmajjfiibgnejlndfoboojahlclgoam)
2. Click **"Add to Chrome"**
3. Configure API keys in the extension settings

### From Source

```bash
git clone https://github.com/igrigorik/AgentBoard.git
cd AgentBoard

npm install
npm run build

# Load in Chrome:
# 1. Navigate to chrome://extensions
# 2. Enable "Developer mode"
# 3. Click "Load unpacked"
# 4. Select the dist/ directory
```

## Configuration

### API Keys

Open AgentBoard settings and configure providers:

```
Settings → Providers
├── OpenAI: sk-...
├── Anthropic: sk-ant-...
├── Google: AIza...
└── Ollama: http://localhost:11434 (no key needed)
```

### WebMCP Integration

AgentBoard automatically detects WebMCP tools when visiting a page. No additional configuration needed:

1. Navigate to a WebMCP-enabled page
2. Open AgentBoard sidebar (click extension icon or use keyboard shortcut)
3. Available tools appear in the "Tools" section
4. Ask the AI to use page tools naturally

## Architecture

```
┌──────────────────────────────────────────┐
│  AgentBoard Chrome Extension             │
│                                          │
│  ┌────────────────┐  ┌───────────────┐   │
│  │  Sidebar UI    │  │  Settings     │   │
│  │  (Chat + Tools)│  │  (Keys, MCP)  │   │
│  └───────┬────────┘  └───────────────┘   │
│          │                               │
│  ┌───────▼────────┐  ┌───────────────┐   │
│  │  LLM Router    │  │  MCP Client   │   │
│  │  (Multi-prov.) │  │  (Remote)     │   │
│  └───────┬────────┘  └───────┬───────┘   │
│          │                   │           │
└──────────┼───────────────────┼───────────┘
           │                   │
    ┌──────▼──────┐   ┌───────▼───────┐
    │  LLM APIs   │   │  Page Context │
    │  (OpenAI,   │   │  (WebMCP      │
    │   Claude,   │   │   Tools)      │
    │   Gemini,   │   │               │
    │   Ollama)   │   │               │
    └─────────────┘   └───────────────┘
```

## Commerce Focus (Shopify Perspective)

Given Ilya Grigorik's role at Shopify, AgentBoard has particular relevance for e-commerce:

- AI agents interacting with product pages via WebMCP tools
- Structured product data extraction for comparison shopping
- Agent-to-agent commerce scenarios (spec issue [#31](https://github.com/webmachinelearning/webmcp/issues/31))
- WebMCP tools for cart management, checkout, and order tracking

## Comparison with Other Extensions

| Feature | AgentBoard | MCP-B Extension | Model Context Tool Inspector |
|---------|-----------|----------------|------------------------------|
| Multi-provider LLM | ✅ (4 providers) | ✅ (via config) | ✅ (Gemini only) |
| WebMCP tool discovery | ✅ | ✅ | ✅ |
| Remote MCP servers | ✅ | ❌ | ❌ |
| Scriptable tools | ✅ | ❌ | ❌ |
| Command templates | ✅ | ❌ | ❌ |
| Local models (Ollama) | ✅ | ❌ | ❌ |
| Open source | ✅ (MIT) | ✅ (AGPL) | ✅ (Apache-2.0) |

## Requirements

- Chrome 100+ (works with or without native WebMCP)
- At least one LLM API key (or Ollama for local models)
- WebMCP flag enabled in Chrome 146+ for native tool discovery

## See Also

- [Agentic Web Learning Tool](./agentic-web-learning-tool.md) — Another Chrome extension for agentic AI
- [WebMCP Extension](./webmcp-extension.md) — Standalone WebMCP extension
- [Model Context Tool Inspector](../official-tools/model-context-tool-inspector.md) — Official tool inspector
- [MiguelsPizza/WebMCP](../polyfills/miguels-pizza-webmcp.md) — MCP-B reference implementation
- [Agent-to-Agent Commerce (Issue #31)](https://github.com/webmachinelearning/webmcp/issues/31) — Spec discussion
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Extension API architecture](../../07-architecture/extension-api.md) — Extension API architecture
- [Human-in-the-loop patterns](../../06-user-interaction/human-in-the-loop.md) — Human-in-the-loop patterns
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [WebExtensions discussion](../../13-spec-discussions/webextensions-external-agents.md) — WebExtensions discussion
- [Threat model overview](../../04-security/threat-model-overview.md) — Threat model overview
- [Agentic web landscape](../../14-industry/agentic-web-landscape.md) — Agentic web landscape
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
