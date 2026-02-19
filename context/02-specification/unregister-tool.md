# unregisterTool() and clearContext() — Removing Tools

## Overview

WebMCP provides two methods for removing tools from the registered set
(see [spec](https://webmachinelearning.github.io/webmcp)):

- **`unregisterTool(name)`** — Removes a single tool by its name
- **`clearContext()`** — Removes all tools and context at once

## WebIDL

```webidl
[Exposed=Window, SecureContext]
interface ModelContext {
  undefined unregisterTool(DOMString name);
  undefined clearContext();
};
```

---

## unregisterTool(name)

### Signature

```webidl
undefined unregisterTool(DOMString name);
```

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | `DOMString` | Yes | The name of the tool to remove from the registered set |

### Return Value

`undefined`

### Behavior

From the [spec](https://webmachinelearning.github.io/webmcp):

> Removes the tool with the specified name from the registered set.

Step-by-step (see [Chromium implementation](https://chromium.googlesource.com/chromium/src/+/a6dd386ea2457906e4566eb6c7b6ca031194c1ad)):

1. Look up the tool with the given `name` in the registered tool set.
2. If found, remove it from the set.
3. Notify connected agents that the tool list has been updated.
4. If no tool with that name exists, the behavior is a no-op (no error thrown).

### Basic Example

```javascript
// Register a tool
navigator.modelContext.registerTool({
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
});

// Later, remove it
navigator.modelContext.unregisterTool("add-todo");
```

### Use Case: Feature Toggles

```javascript
// Enable/disable tools based on feature flags
function updateTools(features) {
  if (features.advancedSearch) {
    navigator.modelContext.registerTool({
      name: "advanced-search",
      description: "Search with filters and facets",
      inputSchema: { /* ... */ },
      execute: advancedSearchHandler
    });
  } else {
    navigator.modelContext.unregisterTool("advanced-search");
  }
}
```

### Use Case: Permission-Based Tool Access

```javascript
// Remove destructive tools when user loses permissions
auth.onPermissionChange((permissions) => {
  if (!permissions.includes("admin")) {
    navigator.modelContext.unregisterTool("delete-user");
    navigator.modelContext.unregisterTool("reset-database");
  }
});
```

### Use Case: Lifecycle Management

```javascript
// Register a tool for a specific workflow step, then remove it
async function processCheckout() {
  navigator.modelContext.registerTool({
    name: "confirm-order",
    description: "Confirm and place the current order",
    execute: async (input, client) => {
      const confirmed = await client.requestUserInteraction(async () => {
        return confirm("Place order for $49.99?");
      });
      if (!confirmed) throw new Error("Order cancelled");
      return { content: [{ type: "text", text: "Order placed!" }] };
    }
  });

  // ... wait for agent or user interaction ...

  // Cleanup after checkout completes
  navigator.modelContext.unregisterTool("confirm-order");
}
```

---

## clearContext()

### Signature

```webidl
undefined clearContext();
```

### Parameters

None.

### Return Value

`undefined`

### Behavior

From the [spec](https://webmachinelearning.github.io/webmcp):

> Unregisters all context (tools) with the browser.

Step-by-step:

1. Remove all tools from the registered set.
2. Clear any other context associated with the model context.
3. Notify connected agents that all tools have been removed.

### Basic Example

```javascript
// Register some tools
navigator.modelContext.provideContext({
  tools: [
    { name: "search", description: "Search", execute: searchFn },
    { name: "filter", description: "Filter", execute: filterFn },
    { name: "sort", description: "Sort", execute: sortFn }
  ]
});

// Remove everything
navigator.modelContext.clearContext();
// Now no tools are registered
```

### Use Case: Page Cleanup

```javascript
// Clear tools when user signs out
function signOut() {
  navigator.modelContext.clearContext();
  redirectToLogin();
}
```

### Use Case: Modal/Overlay States

```javascript
// Disable agent tools during sensitive UI states
function showPaymentModal() {
  // Save current tool state
  const savedTools = getCurrentTools();

  // Clear all tools during payment
  navigator.modelContext.clearContext();

  paymentModal.show();
  paymentModal.onClose(() => {
    // Restore tools after payment modal closes
    navigator.modelContext.provideContext({ tools: savedTools });
  });
}
```

---

## Comparison: unregisterTool vs clearContext vs provideContext({})

| Method | Removes | Adds | Use Case |
|---|---|---|---|
| `unregisterTool("name")` | One specific tool | Nothing | Remove individual tools |
| `clearContext()` | All tools | Nothing | Complete teardown |
| `provideContext({})` | All tools | Nothing | Same as clearContext |
| `provideContext({ tools })` | All tools | New tool set | Full replacement |

### Decision Guide

```
Need to remove just one tool?
  → unregisterTool("tool-name")

Need to remove everything?
  → clearContext()

Need to replace all tools with a new set?
  → provideContext({ tools: [...] })
```

## Interaction with registerTool

The `unregisterTool` / `registerTool` pair is designed for granular tool management:

```javascript
// Dynamic tool management pattern
class ToolManager {
  #tools = new Map();

  register(tool) {
    if (this.#tools.has(tool.name)) {
      navigator.modelContext.unregisterTool(tool.name);
    }
    navigator.modelContext.registerTool(tool);
    this.#tools.set(tool.name, tool);
  }

  unregister(name) {
    navigator.modelContext.unregisterTool(name);
    this.#tools.delete(name);
  }

  replaceAll(tools) {
    navigator.modelContext.provideContext({ tools });
    this.#tools.clear();
    for (const tool of tools) {
      this.#tools.set(tool.name, tool);
    }
  }

  clear() {
    navigator.modelContext.clearContext();
    this.#tools.clear();
  }
}
```

## Edge Cases

### Unregistering a Non-Existent Tool

```javascript
// This is a no-op — no error thrown
navigator.modelContext.unregisterTool("nonexistent-tool");
```

### Clearing When Already Empty

```javascript
// This is a no-op — no error thrown
navigator.modelContext.clearContext();
navigator.modelContext.clearContext(); // Second call is also fine
```

### Registering After Clear

```javascript
navigator.modelContext.clearContext();

// Can still register new tools after clearing
navigator.modelContext.registerTool({
  name: "new-tool",
  description: "A fresh tool",
  execute: async () => ({
    content: [{ type: "text", text: "Hello from the new tool!" }]
  })
});
```

## Related Issues

- [Issue #15 — Dual registration](https://github.com/webmachinelearning/webmcp/issues/15) — registerTool/unregisterTool vs provideContext design
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — HTML-based tool lifecycle

## See Also

- [Specification overview](spec-overview.md)
- [registerTool() method](register-tool.md)
- [provideContext() method](provide-context.md)
- [navigator.modelContext API](navigator-model-context.md)
- [WebIDL definitions](webidl-definitions.md)
- [WebMCP events](events.md)
- [Tool registration interface](tool-registration-interface.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Service workers overview](../05-service-workers/overview.md)
- [Iframe tool registration](../07-architecture/iframe-registration.md)
- [API reference](../15-reference/api-reference.md)
