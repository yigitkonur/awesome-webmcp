# navigator.modelContext — The Root API Object

## Overview

The `navigator.modelContext` property is the entry point to the entire [WebMCP API](https://webmachinelearning.github.io/webmcp). It
returns a `ModelContext` object through which web applications register and manage tools
that can be invoked by AI agents.

## WebIDL Definition

From [PR #75](https://github.com/webmachinelearning/webmcp/pull/75):

```webidl
partial interface Navigator {
  [SecureContext, SameObject] readonly attribute ModelContext modelContext;
};
```

## How It Extends the Navigator Interface

WebMCP extends the existing `Navigator` interface with a single new attribute (see [spec](https://webmachinelearning.github.io/webmcp)). This follows
the established web platform pattern used by other APIs:

| API | Navigator Property | Pattern |
|---|---|---|
| Geolocation | `navigator.geolocation` | `[SameObject] readonly attribute` |
| Service Workers | `navigator.serviceWorker` | `[SameObject] readonly attribute` |
| Clipboard | `navigator.clipboard` | `[SecureContext, SameObject] readonly attribute` |
| **WebMCP** | **`navigator.modelContext`** | **`[SecureContext, SameObject] readonly attribute`** |

## Attribute Properties

### SecureContext

The `[SecureContext]` extended attribute means `navigator.modelContext` is only available
in [secure contexts](https://w3c.github.io/webappsec-secure-contexts/) — HTTPS origins
and `localhost`. This is a security requirement because:

- Tools may expose sensitive application functionality to AI agents
- Tool execution can modify application state including user data
- The browser mediates access between untrusted agents and page content
- Without HTTPS, man-in-the-middle attacks could inject malicious tools

```javascript
// On HTTP — navigator.modelContext is undefined
if (!("modelContext" in navigator)) {
  console.warn("WebMCP requires a secure context (HTTPS)");
}

// On HTTPS — navigator.modelContext is available
navigator.modelContext.provideContext({
  tools: [/* ... */]
});
```

### SameObject

The `[SameObject]` extended attribute guarantees that every access to
`navigator.modelContext` returns the same JavaScript object identity:

```javascript
const ctx1 = navigator.modelContext;
const ctx2 = navigator.modelContext;
console.log(ctx1 === ctx2); // true — always the same object
```

This enables identity-based comparisons and ensures that tool registrations persist
across multiple accesses to the property.

### readonly

The attribute is read-only — you cannot replace `navigator.modelContext` with a
different object:

```javascript
navigator.modelContext = {}; // TypeError in strict mode, silently fails otherwise
```

## Why navigator Was Chosen (Issue #24)

The naming of the global entry point went through significant discussion in
[Issue #24 — Bikeshedding the global name](https://github.com/webmachinelearning/webmcp/issues/24).

### Evolution of the Name

| Stage | Name | Rationale |
|---|---|---|
| Initial proposal | `window.agent` | Represented the agent interacting with the page |
| Alternative | `window.mcp` | Aligned with Model Context Protocol name |
| Final spec | `navigator.modelContext` | Web platform convention + context-focused naming |

### Why `window.agent` Was Rejected

From Issue #24 (Brandon Walderman):

> `agent` (singular) may not be the best choice of name since it assumes there's only one
> agent. Support for multiple different agent clients has come up recently in issues like
> #20, #16. Even a single agent product (i.e. Claude, Copilot, Gemini) can have multiple
> different conversations which can be thought of as unique clients from the page's point
> of view.

Key reasons `window.agent` was problematic:
1. **Implies singular agent** — Multiple agents may connect to one page
2. **Wrong semantic direction** — The page provides context, it doesn't own the agent
3. **Naming collision risk** — `window.agent` is a common variable name in web apps

### Why navigator.modelContext Was Chosen

1. **`navigator` is the established namespace** for browser capability APIs (geolocation, clipboard, service workers, credentials, media devices)
2. **`modelContext`** focuses on what the page provides (context for models) rather than who consumes it (agents)
3. **Future-proof** — Doesn't presuppose a single agent, protocol, or interaction pattern
4. **Descriptive** — Clearly indicates the API is about providing context to AI models

## Feature Detection

Web developers should feature-detect WebMCP before using it. The [Chrome Early Preview](https://developer.chrome.com/blog/webmcp-epp) is the first browser to support the API:

```javascript
// Recommended pattern
if ("modelContext" in navigator) {
  navigator.modelContext.provideContext({
    tools: [
      {
        name: "search",
        description: "Search the product catalog",
        inputSchema: {
          type: "object",
          properties: {
            query: { type: "string", description: "Search query" }
          },
          required: ["query"]
        },
        execute: async ({ query }) => {
          const results = await searchProducts(query);
          return { content: [{ type: "text", text: JSON.stringify(results) }] };
        }
      }
    ]
  });
} else {
  console.log("WebMCP is not supported in this browser");
}
```

## Scope: Top-Level Browsing Context Only

From the [API proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> Only a top-level browsing context, such as a browser tab can be a model context provider.

This means:
- **Tabs** — ✅ Can register tools
- **iframes** — ❌ Cannot directly register tools (security boundary)
- **Workers** — See [service worker extension proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) for background tool handling
- **Popups** — Depends on browsing context type

## The ModelContext Object

Once accessed, `navigator.modelContext` returns a `ModelContext` interface with four methods:

```javascript
const mc = navigator.modelContext;

// Register all tools at once (clears previous set)
mc.provideContext({ tools: [...] });

// Register a single tool (additive)
mc.registerTool({ name: "add-item", /* ... */ });

// Remove a specific tool
mc.unregisterTool("add-item");

// Remove all tools
mc.clearContext();
```

See dedicated documentation for each method:
- [provideContext()](./provide-context.md)
- [registerTool()](./register-tool.md)
- [unregisterTool() / clearContext()](./unregister-tool.md)

## Thread Model

Tool callbacks execute on the **main thread** of the top-level browsing context:

> Handling tool calls in the main thread with the option of delegating to workers serves
> a few purposes:
> - Ensures tool calls run one at a time and sequentially.
> - The page can update UI to reflect state changes performed by tools.
> - Handling tool calls in page script may be sufficient for simple applications.

For long-running or computationally heavy operations, tools should delegate to
[Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API):

```javascript
navigator.modelContext.registerTool({
  name: "analyze-data",
  description: "Run heavy data analysis",
  inputSchema: { type: "object", properties: { dataset: { type: "string" } } },
  execute: async ({ dataset }) => {
    // Delegate to worker for heavy computation
    const worker = new Worker("analysis-worker.js");
    return new Promise((resolve) => {
      worker.onmessage = (e) => resolve({
        content: [{ type: "text", text: e.data.result }]
      });
      worker.postMessage({ dataset });
    });
  }
});
```

## Lifecycle

The `modelContext` is available as long as the browsing context (tab) exists. Tools
registered through it persist until:

1. The page navigates away (new page load clears all tools)
2. `clearContext()` is called
3. `provideContext()` is called (replaces all tools)
4. Individual tools are removed with `unregisterTool()`

## Related Issues

- [Issue #24 — Bikeshedding the global name](https://github.com/webmachinelearning/webmcp/issues/24) — Name evolution discussion
- [Issue #20 — User takeover](https://github.com/webmachinelearning/webmcp/issues/20) — Multi-agent considerations
- [Issue #16 — External agents](https://github.com/webmachinelearning/webmcp/issues/16) — Agent client identity

## See Also

- [Specification overview](spec-overview.md)
- [registerTool() method](register-tool.md)
- [provideContext() method](provide-context.md)
- [unregisterTool() and clearContext()](unregister-tool.md)
- [WebIDL definitions](webidl-definitions.md)
- [WebMCP events](events.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Glossary of terms](../01-overview/glossary.md)
- [Declarative API overview](../03-declarative-api/overview.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)
- [API reference](../15-reference/api-reference.md)
