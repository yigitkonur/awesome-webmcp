# WebMCP Polyfill Guide

> How to use WebMCP polyfills to develop and test WebMCP tools in browsers that don't natively support the API — including `@mcp-b/global` and community polyfills.

---

## Overview

WebMCP is currently only available natively in Chrome Canary 146+ behind a flag. For development and testing in other browsers — or in Chrome versions without the flag — polyfills can provide a compatibility layer.

---

## Why Polyfills?

| Scenario | Polyfill Helps |
|----------|----------------|
| Development on Firefox or Safari | ✅ Test tool registration and execution logic |
| CI/CD testing environments | ✅ Run automated tests without Chrome Canary |
| Cross-browser compatibility layer | ✅ Deploy tools that work everywhere |
| Production fallback | ✅ Graceful degradation for unsupported browsers |

---

## MCP-B / WebMCP Community Polyfill

The [MCP-B project](https://mcp-b.ai/) (Model Context Protocol for the Browser) by Alex Nahas was one of the first implementations that inspired the official WebMCP spec. It provides an open-source polyfill that emulates the `navigator.modelContext` API.

### Source Repository

- npm packages: [WebMCP-org/npm-packages](https://github.com/WebMCP-org/npm-packages) (MIT license)
- Parent project: [MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP) (AGPL-3.0)

### Installation

```bash
npm install @mcp-b/global
```

Or include via CDN in your HTML:

```html
<script src="https://unpkg.com/@mcp-b/global/dist/index.global.js"></script>
```

### Basic Usage

```js
// Import the polyfill (if using modules)
import '@mcp-b/global';

// The polyfill adds navigator.modelContext if not already present
// Now you can use the standard API:
if ("modelContext" in navigator) {
  navigator.modelContext.registerTool({
    name: "greet",
    description: "Greet someone by name",
    inputSchema: {
      type: "object",
      properties: {
        name: { type: "string", description: "Person's name" }
      },
      required: ["name"]
    },
    execute({ name }) {
      return {
        content: [{
          type: "text",
          text: `Hello, ${name}!`
        }]
      };
    }
  });
}
```

### How the Polyfill Works

The polyfill:
1. **Checks** if `navigator.modelContext` already exists (native browser support)
2. If not, **creates** a polyfill implementation on `navigator.modelContext`
3. **Implements** the core API: `provideContext`, `registerTool`, `unregisterTool`, `clearContext`
4. **Stores** registered tools in memory
5. **Exposes** tools to compatible browser extensions or external agents via messaging

### API Compatibility

| API Method | Native Chrome | MCP-B Polyfill |
|-----------|---------------|----------------|
| `provideContext({ tools })` | ✅ | ✅ |
| `registerTool(descriptor)` | ✅ | ✅ |
| `unregisterTool(name)` | ✅ | ✅ |
| `clearContext()` | ✅ | ✅ |
| `execute` callback | ✅ | ✅ |
| `agent.requestUserInteraction` | ✅ | ⚠️ Partial |
| Declarative form tools | ✅ | ❌ Not supported |
| `:tool-form-active` CSS | ✅ | ❌ Not supported |
| `SubmitEvent.agentInvoked` | ✅ | ❌ Not supported |
| `toolactivated` event | ✅ | ❌ Not supported |

---

## Building Your Own Polyfill

For minimal custom polyfills, you can implement the core interface:

```js
// Minimal WebMCP polyfill
if (!("modelContext" in navigator)) {
  const tools = new Map();

  navigator.modelContext = {
    provideContext({ tools: toolList }) {
      tools.clear();
      for (const tool of toolList) {
        tools.set(tool.name, tool);
      }
      console.log(`[WebMCP Polyfill] Registered ${toolList.length} tools`);
    },

    registerTool(descriptor) {
      tools.set(descriptor.name, descriptor);
      console.log(`[WebMCP Polyfill] Registered tool: ${descriptor.name}`);
    },

    unregisterTool(name) {
      tools.delete(name);
      console.log(`[WebMCP Polyfill] Unregistered tool: ${name}`);
    },

    clearContext() {
      tools.clear();
      console.log("[WebMCP Polyfill] All tools cleared");
    },

    // Helper for testing — not part of official API
    _getTools() {
      return Array.from(tools.values());
    },

    // Helper for testing — invoke a tool programmatically
    async _invokeTool(name, params) {
      const tool = tools.get(name);
      if (!tool) throw new Error(`Tool "${name}" not found`);
      return await tool.execute(params, {
        requestUserInteraction: async (callback) => await callback()
      });
    }
  };

  console.log("[WebMCP Polyfill] Initialized on navigator.modelContext");
}
```

### Usage with Test Helpers

```js
// In your test environment:
const result = await navigator.modelContext._invokeTool("greet", { name: "World" });
console.log(result.content[0].text); // "Hello, World!"

// List registered tools:
console.log(navigator.modelContext._getTools().map(t => t.name));
// ["greet"]
```

---

## Progressive Enhancement Pattern

Always use feature detection to handle both native and polyfilled environments:

```js
// Feature detection — works with both native and polyfill
function initWebMCP() {
  if (!("modelContext" in navigator)) {
    console.warn("WebMCP not available (no native support or polyfill loaded)");
    return;
  }

  // Register tools — same code works for native and polyfilled
  navigator.modelContext.registerTool({
    name: "my-tool",
    description: "Does something useful",
    inputSchema: {
      type: "object",
      properties: {
        input: { type: "string", description: "The input" }
      },
      required: ["input"]
    },
    execute({ input }) {
      return {
        content: [{ type: "text", text: `Processed: ${input}` }]
      };
    }
  });
}

// Call after polyfill has loaded (if using one)
initWebMCP();
```

### Loading Order

```html
<!-- Option 1: Load polyfill first, then your code -->
<script src="https://unpkg.com/@mcp-b/global/dist/index.global.js"></script>
<script src="your-webmcp-tools.js"></script>

<!-- Option 2: Conditional polyfill loading -->
<script>
  if (!("modelContext" in navigator)) {
    const script = document.createElement('script');
    script.src = 'https://unpkg.com/@mcp-b/global/dist/index.global.js';
    script.onload = () => initWebMCP();
    document.head.appendChild(script);
  } else {
    initWebMCP();
  }
</script>
```

---

## Limitations of Polyfills

### What Polyfills Cannot Do

1. **Browser-mediated agent connection**: Native WebMCP connects to the browser's built-in agent; polyfills need a different agent connection mechanism
2. **Declarative form tools**: The `toolname` / `tooldescription` HTML attributes require browser-level parsing
3. **CSS pseudo-classes**: `:tool-form-active` and `:tool-submit-active` are browser-native
4. **SubmitEvent properties**: `agentInvoked` and `respondWith()` require browser modifications
5. **Permission prompts**: Native WebMCP shows browser-level permission dialogs
6. **Cross-origin isolation**: Browser-level security policies for tool data

### What Polyfills CAN Do

1. **Tool registration and execution** — Core API works fully
2. **Schema validation** — inputSchema can be validated client-side
3. **Tool discovery** — Extensions can read registered tools
4. **Testing and development** — Full development workflow support
5. **Code reuse** — Same tool code works with native and polyfill

---

## Cross-Browser Development Workflow

```
Development:
  ├── Write tools once using standard API
  ├── Test in Chrome Canary (native)
  ├── Test in Firefox/Safari (polyfill)
  └── Run automated tests (polyfill in CI)

Production:
  ├── Chrome 146+: Native WebMCP
  ├── Other browsers: Polyfill + extension-based agent
  └── No agent available: Tools gracefully no-op
```

### Example CI Configuration

```yaml
# GitHub Actions example
test-webmcp:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - name: Install dependencies
      run: npm install
    - name: Run WebMCP tests with polyfill
      run: npm test
      env:
        WEBMCP_POLYFILL: true
```

---

## See Also

- [Chrome Setup Guide](./chrome-setup.md) — Native Chrome setup
- [Getting Started](./getting-started.md) — First tool creation
- [Testing Tools](./testing-tools.md) — Testing patterns
- [What Is WebMCP](../01-overview/what-is-webmcp.md) — Core concepts
- [Browser support status](../01-overview/browser-support.md) — Browser support status
- [MCP-B polyfill](../08-ecosystem/polyfills/mcp-b-global.md) — MCP-B polyfill
- [Miguel's Pizza polyfill](../08-ecosystem/polyfills/miguels-pizza-webmcp.md) — Miguel's Pizza polyfill
- [Ripulio polyfill](../08-ecosystem/polyfills/ripulio-web-mcp.md) — Ripulio polyfill
- [OpenTiny Next SDK](../08-ecosystem/polyfills/opentiny-next-sdk.md) — OpenTiny Next SDK
- [Mozilla's position on WebMCP](../14-industry/mozilla-position.md) — Mozilla's position on WebMCP
- [Apple Safari support status](../14-industry/apple-safari-status.md) — Apple Safari support status
- [Complete API reference](../15-reference/api-reference.md) — Complete API reference
