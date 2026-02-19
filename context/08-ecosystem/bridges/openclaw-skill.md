# 6missedcalls/openclaw-webmcp-skill

> OpenClaw skill for discovering and invoking `navigator.modelContext` tools without DOM scraping.

## Overview

[6missedcalls/openclaw-webmcp-skill](https://github.com/6missedcalls/openclaw-webmcp-skill) is an OpenClaw skill that enables AI agents to discover and invoke WebMCP tools registered on web pages through the `navigator.modelContext` API — without resorting to DOM scraping, screenshot parsing, or other fragile automation techniques.

- **Repository**: [github.com/6missedcalls/openclaw-webmcp-skill](https://github.com/6missedcalls/openclaw-webmcp-skill)
- **Author**: [@6missedcalls](https://github.com/6missedcalls)
- **License**: MIT
- **Platform**: OpenClaw AI agent framework

## The Problem

Traditional AI browser agents interact with web pages by:
1. Taking screenshots and parsing them with vision models
2. Scraping the DOM to find interactive elements
3. Injecting click/type events to simulate user actions

These approaches are **slow, fragile, and expensive**. WebMCP provides a structured alternative where websites explicitly declare their capabilities, but AI agent frameworks need specific integration to use them.

## The Solution

openclaw-webmcp-skill adds WebMCP awareness to OpenClaw agents:

```
Traditional Agent:           WebMCP-Aware Agent:
  Screenshot → Parse →        Tool Discovery →
  Find Button → Click →       Tool Invocation →
  Wait → Screenshot →         Structured Response
  Parse Result               
  
  ~10 steps, fragile          ~2 steps, reliable
```

## Key Features

### Tool Discovery

Discovers all tools registered via `navigator.modelContext`:

```javascript
// The skill adds this capability to OpenClaw agents
const tools = await agent.discoverWebMCPTools();
// Returns:
// [
//   { name: "searchFlights", description: "Search for flights", inputSchema: {...} },
//   { name: "bookFlight", description: "Book a flight", inputSchema: {...} },
//   { name: "getFlightDetails", description: "Get flight info", inputSchema: {...} }
// ]
```

### Tool Invocation

Invoke discovered tools with validated parameters:

```javascript
const result = await agent.invokeWebMCPTool("searchFlights", {
  origin: "LAX",
  destination: "JFK",
  date: "2026-03-20"
});
// Returns structured tool response instead of DOM content
```

### Declarative Tool Detection

Also discovers tools defined via HTML attributes:

```html
<!-- Skill detects these declarative tools -->
<form toolname="subscribe" tooldescription="Subscribe to newsletter">
  <input type="email" name="email" required toolparamdescription="Email address">
  <button type="submit">Subscribe</button>
</form>
```

### No DOM Scraping

Unlike traditional browser automation:

| Approach | DOM Scraping | WebMCP Skill |
|----------|-------------|--------------|
| Discovery | Parse entire DOM | `navigator.modelContext` API |
| Invocation | Simulate clicks/types | Call `execute()` handler |
| Response | Parse DOM changes | Structured JSON response |
| Reliability | Fragile (layout-dependent) | Robust (schema-validated) |
| Speed | Slow (render + parse) | Fast (direct function call) |
| Token cost | High (DOM/screenshot) | Low (structured data) |

## Installation

### As an OpenClaw Skill

```bash
# Install the skill
openclaw install skill @6missedcalls/openclaw-webmcp-skill

# Or add to your OpenClaw agent config
```

### Configuration

```yaml
# openclaw.config.yaml
skills:
  - name: webmcp
    package: "@6missedcalls/openclaw-webmcp-skill"
    config:
      preferWebMCP: true       # Prefer WebMCP over DOM scraping
      fallbackToDOM: true      # Fall back to DOM if no WebMCP tools
      timeout: 30000           # Tool execution timeout (ms)
      validateInputs: true     # Validate params against inputSchema
```

## Usage in OpenClaw Agents

### Basic Usage

```javascript
import { Agent } from 'openclaw';
import { webmcpSkill } from '@6missedcalls/openclaw-webmcp-skill';

const agent = new Agent({
  skills: [webmcpSkill()]
});

// Agent navigates to a page
await agent.navigate("https://travel-demo.bandarra.me/");

// Agent discovers WebMCP tools
const tools = await agent.discoverWebMCPTools();

// Agent uses tools instead of DOM manipulation
if (tools.find(t => t.name === "searchFlights")) {
  const result = await agent.invokeWebMCPTool("searchFlights", {
    origin: "SFO",
    destination: "NRT",
    date: "2026-04-15"
  });
  console.log("Flights found:", result);
}
```

### Hybrid Mode

Falls back to DOM automation when WebMCP tools aren't available:

```javascript
const agent = new Agent({
  skills: [
    webmcpSkill({ fallbackToDOM: true })
  ]
});

// Agent tries WebMCP first, falls back to DOM if needed
await agent.perform("Search for flights from SFO to London");
// 1. Check for navigator.modelContext tools → found? Use them
// 2. No WebMCP tools → fall back to DOM scraping/clicking
```

## Supported OpenClaw Features

| Feature | Supported |
|---------|-----------|
| Tool discovery | ✅ |
| Tool invocation | ✅ |
| Parameter validation | ✅ |
| Annotation reading | ✅ |
| User interaction requests | ✅ |
| Declarative tool detection | ✅ |
| Event handling | ✅ |
| Multi-tab support | ✅ |

## Architecture

```
┌──────────────────────────────────────┐
│  OpenClaw Agent                      │
│  ┌────────────────────────────────┐  │
│  │  Skills Layer                  │  │
│  │  ┌──────────────────────────┐  │  │
│  │  │  WebMCP Skill            │  │  │
│  │  │  - discoverTools()       │  │  │
│  │  │  - invokeTool()          │  │  │
│  │  │  - detectDeclarative()   │  │  │
│  │  └──────────┬───────────────┘  │  │
│  └─────────────┼──────────────────┘  │
│                │                     │
│  ┌─────────────▼──────────────────┐  │
│  │  Browser Context               │  │
│  │  navigator.modelContext        │  │
│  │  (Native or Polyfill)          │  │
│  └────────────────────────────────┘  │
└──────────────────────────────────────┘
```

## See Also

- [WebMCP Adapter](./webmcp-adapter.md) — Auto-discovery adapter for frontend actions
- [WebMCP Bridge CLI](./webmcp-bridge-cli.md) — Bridge to MCP clients
- [three-water666/WebMCP](./three-water-webmcp.md) — Bridge web AI to VS Code
- [Travel Demo](../demos/travel-demo.md) — Official Chrome team demo (good test page)
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [WebMCP vs server-side MCP](../../01-overview/webmcp-vs-server-mcp.md) — WebMCP vs server-side MCP
- [MCP protocol intersection](../../02-specification/mcp-intersection.md) — MCP protocol intersection
- [MCP protocol alignment](../../07-architecture/mcp-protocol-alignment.md) — MCP protocol alignment
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Agentic web landscape](../../14-industry/agentic-web-landscape.md) — Agentic web landscape
- [Enterprise workflow use case](../../11-use-cases/enterprise-workflows.md) — Enterprise workflow use case
