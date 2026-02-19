# WebMCP and MCP Protocol Intersection

## Overview

WebMCP is intentionally designed to align with the [Model Context Protocol (MCP)](https://modelcontextprotocol.io/introduction) while
adapting it for the browser environment. This intersection was formalized in
[PR #36](https://github.com/webmachinelearning/webmcp/pull/36). This document explains how WebMCP relates to
MCP, where they align, where they differ, and how the browser mediates between them.

## What Is MCP?

From the Bikeshed spec bibliography:

> **Model Context Protocol (MCP) Specification**
> Publisher: The Linux Foundation
> URL: https://modelcontextprotocol.io/specification/latest

MCP is a layered protocol enabling client-server communication for AI agents. From
the [WebMCP proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> MCP is a layered protocol enabling client-server communication. The client owns the
> AI Agent connecting to external systems using this protocol and the server is the
> external system.

### MCP Layers

| Layer | Description | WebMCP Equivalent |
|---|---|---|
| **Primitives** | Tools, Resources, Prompts | WebMCP implements Tools |
| **Data Layer** | Control messages (`tools/list`, `tools/call`) | Browser handles internally |
| **Transport Layer** | HTTP POST, SSE, stdio | Not applicable (in-browser) |

## Design Philosophy

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> This proposal aligns the Web API closely with MCP primitives. This ensures agentic
> capabilities on the Web declared via WebMCP can be used by any MCP compatible Agent
> with minimal translation layers; and makes it easier for web authors to reuse code
> with their MCP service.

### Key Design Decision: Browser as Intermediary

Rather than exposing raw MCP protocol messages to web pages, the browser acts as an
intermediary for the data layer:

> Implementation of the data layer to arbitrate access to these primitives for an Agent
> is left to the browser.

## Advantages of Browser Mediation

### 1. Protocol Version Decoupling

> It doesn't directly couple the Web to a specific MCP version. The fact that the control
> flow is intermediated by the browser, instead of being opaque messages exchanged between
> the site and the Agent, allows the browser to maintain backwards compatibility as the
> protocol evolves.

```
MCP v1.0 Agent  ←→  Browser  ←→  WebMCP Page
MCP v2.0 Agent  ←→  Browser  ←→  WebMCP Page (same API, no changes needed)
```

The browser translates between whatever MCP version the agent speaks and the stable
WebMCP API the page implements. Web developers don't need to update their code when
MCP evolves.

### 2. Web Security Policies

> The browser can apply security policies unique to the web platform. For example,
> embedders would need to manage the capabilities provided to iframes.

Browser security features that apply to WebMCP:

| Security Feature | How It Applies |
|---|---|
| **Same-origin policy** | Tools are scoped to the registering origin |
| **HTTPS requirement** | `[SecureContext]` attribute on the API |
| **CSP (Content Security Policy)** | Tool callbacks subject to page CSP |
| **Iframe sandboxing** | Embedders control iframe tool access |
| **Permission model** | Browser mediates agent-to-tool access |
| **User consent** | Browser can require user approval for tool calls |

### 3. Web Platform API Ergonomics

> The API ergonomics can align with the Web platform. For example, tool response can use
> `img` or `video` elements for multi-modal output.

WebMCP leverages web platform capabilities unavailable in MCP:

| Feature | MCP | WebMCP |
|---|---|---|
| Tool implementation | Server-side code | Client-side JavaScript |
| UI updates | Not possible | Direct DOM manipulation |
| User interaction | Not built-in | `requestUserInteraction()` |
| Media output | Base64 encoded | Native `img`/`video` elements |
| Form integration | N/A | HTML forms as tools |
| CSS styling | N/A | `:tool-form-active` pseudo-class |

### 4. Declarative Counterpart

> There can be a declarative counterpart to imperative tools.

See [Issue #22](https://github.com/webmachinelearning/webmcp/issues/22) and
[Issue #43 — Scope](https://github.com/webmachinelearning/webmcp/issues/43) for the
HTML-based declarative tool definition discussion.

## Alignment with MCP Primitives

### Tools (Implemented)

WebMCP directly implements the MCP tools primitive:

**MCP Tool:**
```json
{
  "name": "add-todo",
  "description": "Add a new todo item",
  "inputSchema": {
    "type": "object",
    "properties": {
      "text": { "type": "string", "description": "Todo text" }
    },
    "required": ["text"]
  }
}
```

**WebMCP Tool:**
```javascript
{
  name: "add-todo",
  description: "Add a new todo item",
  inputSchema: {
    type: "object",
    properties: {
      text: { type: "string", description: "Todo text" }
    },
    required: ["text"]
  },
  execute: ({ text }) => {
    addTodo(text);
    return { content: [{ type: "text", text: `Added: ${text}` }] };
  }
}
```

The tool definition is nearly identical. WebMCP adds:
- `execute` callback (implementation is inline, not on a remote server)
- `annotations` hints (aligned with MCP's annotation concept)

### Resources (Not Yet Implemented)

MCP resources provide static context (files, data) to agents. WebMCP currently focuses
on tools. Resources could be added in the future through the `ModelContextOptions`
dictionary:

```javascript
// Speculative future API
navigator.modelContext.provideContext({
  tools: [/* ... */],
  resources: [
    {
      uri: "stamps://collection",
      name: "Stamp Collection",
      description: "Current stamp database",
      mimeType: "application/json",
      read: async () => JSON.stringify(stamps)
    }
  ]
});
```

### Prompts (Not Yet Implemented)

MCP prompts are templates for system prompts. WebMCP does not currently implement
prompts, but they could be added:

```javascript
// Speculative future API
navigator.modelContext.provideContext({
  tools: [/* ... */],
  prompts: [
    {
      name: "stamp-expert",
      description: "Act as a philately expert helping manage the stamp collection",
      messages: [
        { role: "system", content: "You are a stamp collecting expert..." }
      ]
    }
  ]
});
```

## Data Flow Comparison

### MCP Data Flow

```
Agent (Client) → MCP Protocol → MCP Server → Backend Logic → Response
                  (JSON-RPC)    (Node.js/Python)
```

### WebMCP Data Flow

```
Agent → Browser Process → JavaScript Callback → DOM/API/Workers → Response
         (mediates)       (main thread)
```

### Key Differences

| Aspect | MCP | WebMCP |
|---|---|---|
| **Transport** | HTTP, SSE, stdio | In-process (browser) |
| **Execution** | Server-side | Client-side (browser tab) |
| **Discovery** | Server connection URL | Navigate to page / manifest |
| **State** | Server manages | Page manages (DOM, JS state) |
| **Authentication** | OAuth, API keys | Browser cookies/sessions |
| **Concurrency** | Server handles | Main thread (sequential) |
| **Lifecycle** | Server uptime | Page lifetime |

## Translation Between MCP and WebMCP

An MCP-compatible agent connecting to a WebMCP page would see:

### tools/list → Browser translates to registered tools

```
Agent sends: { method: "tools/list" }

Browser translates to: navigator.modelContext's registered tools

Agent receives: {
  tools: [
    {
      name: "add-stamp",
      description: "Add a new stamp",
      inputSchema: { ... }
    }
  ]
}
```

### tools/call → Browser invokes execute callback

```
Agent sends: {
  method: "tools/call",
  params: {
    name: "add-stamp",
    arguments: { name: "Blue Penny", year: 1847 }
  }
}

Browser calls: tool.execute({ name: "Blue Penny", year: 1847 }, client)

Agent receives: {
  content: [{ type: "text", text: "Stamp added!" }]
}
```

## Code Reuse Between MCP and WebMCP

Developers who maintain both an MCP server and a web frontend can share tool logic:

```javascript
// shared/tools.js — Shared tool logic
export function addStampLogic(name, description, year, imageUrl) {
  return {
    stamp: { name, description, year, imageUrl },
    message: `Stamp "${name}" added successfully!`
  };
}

// mcp-server/tools.js — MCP Server
import { addStampLogic } from "../shared/tools.js";

server.tool("add-stamp", schema, async ({ name, description, year, imageUrl }) => {
  const result = addStampLogic(name, description, year, imageUrl);
  await db.insert(result.stamp);
  return { content: [{ type: "text", text: result.message }] };
});

// web-client/tools.js — WebMCP
import { addStampLogic } from "../shared/tools.js";

navigator.modelContext.registerTool({
  name: "add-stamp",
  description: "Add a new stamp to the collection",
  inputSchema: schema,
  execute: ({ name, description, year, imageUrl }) => {
    const result = addStampLogic(name, description, year, imageUrl);
    stamps.push(result.stamp);
    renderStamps();
    return { content: [{ type: "text", text: result.message }] };
  }
});
```

## When to Use MCP vs. WebMCP

| Scenario | Use MCP | Use WebMCP |
|---|---|---|
| Headless API access | ✅ | ❌ (needs browsing context) |
| Server-side operations | ✅ | ❌ |
| UI interaction required | ❌ | ✅ |
| User confirmation needed | ❌ | ✅ (`requestUserInteraction`) |
| Code reuse with frontend | — | ✅ |
| No backend available | ❌ | ✅ |
| Multiple concurrent agents | ✅ | Limited |
| Discovery without navigation | ✅ | ❌ (future: manifests) |

## Security Differences

| Concern | MCP | WebMCP |
|---|---|---|
| **Auth** | OAuth, API keys, custom | Browser session cookies |
| **Injection** | Server validates | Browser + page validate |
| **Data isolation** | Server controls | Same-origin policy |
| **Permissions** | Server decides | Browser mediates |
| **Transport security** | TLS | HTTPS required (`[SecureContext]`) |

## The Lethal Trifecta

Both MCP and WebMCP face the "lethal trifecta" of agent security risks
(coined by Simon Willison):

1. **Access to private data** — Tools inherently expose functionality
2. **Exposure to untrusted content** — Tool descriptions/outputs can contain injections
3. **Ability to externally communicate** — Agents can act across sites

WebMCP's browser mediation provides additional guardrails compared to raw MCP, but
the fundamental risks remain. See [Issue #11](https://github.com/webmachinelearning/webmcp/issues/11).

## Related Issues

- [PR #36 — MCP intersection](https://github.com/webmachinelearning/webmcp/pull/36) — Formalized MCP alignment
- [Issue #43 — Scope](https://github.com/webmachinelearning/webmcp/issues/43) — Scope of WebMCP relative to MCP
- [Issue #11 — Prompt injection](https://github.com/webmachinelearning/webmcp/issues/11) — Security risks shared with MCP
- [Issue #22 — Declarative API](https://github.com/webmachinelearning/webmcp/issues/22) — Web-native tool definitions
- [Issue #44 — Action-specific permissions](https://github.com/webmachinelearning/webmcp/issues/44) — Browser permission model
- [Issue #53 — Semantic hints](https://github.com/webmachinelearning/webmcp/issues/53) — MCP annotation alignment

## See Also

- [WebMCP vs server-side MCP](../01-overview/webmcp-vs-server-mcp.md)
- [WebMCP vs other protocols](../01-overview/webmcp-vs-other-protocols.md)
- [MCP protocol alignment](../07-architecture/mcp-protocol-alignment.md)
- [Specification overview](spec-overview.md)
- [Tool registration interface](tool-registration-interface.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Security threat model](../04-security/threat-model-overview.md)
- [SDK abstraction layer](../07-architecture/sdk-abstraction.md)
- [MCP bridge protocol](../08-ecosystem/bridges/mcp-bridge-protocol.md)
- [MCP overlap debate](../13-spec-discussions/mcp-overlap-debate.md)
