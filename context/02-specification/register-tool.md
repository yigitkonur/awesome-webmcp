# registerTool() — Register a Single Tool

## Overview

`registerTool()` adds a single tool to the browser's registered tool set **without**
clearing any previously registered tools. It is the additive counterpart to
`provideContext()`, which replaces the entire tool set. Both methods are part of the
[dual registration API](https://github.com/webmachinelearning/webmcp/issues/15) defined
in the [spec](https://webmachinelearning.github.io/webmcp).

## Signature

```webidl
[Exposed=Window, SecureContext]
interface ModelContext {
  undefined registerTool(ModelContextTool tool);
};
```

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `tool` | `ModelContextTool` | Yes | A dictionary describing the tool to register |

### Return Value

`undefined` — The method does not return a value.

### Exceptions

| Exception | Condition |
|---|---|
| `TypeError` | A tool with the same `name` already exists in the registered set |
| `TypeError` | The `inputSchema` property is present but not a valid JSON Schema object |

## ModelContextTool Dictionary

The `tool` parameter must conform to the `ModelContextTool` dictionary:

```webidl
dictionary ModelContextTool {
  required DOMString name;
  required DOMString description;
  object inputSchema;
  required ToolExecuteCallback execute;
  ToolAnnotations annotations;
};
```

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | `DOMString` | ✅ | Unique identifier for the tool |
| `description` | `DOMString` | ✅ | Natural language description of what the tool does |
| `inputSchema` | `object` | ❌ | JSON Schema defining the tool's input parameters |
| `execute` | `ToolExecuteCallback` | ✅ | Callback function invoked when an agent calls the tool |
| `annotations` | `ToolAnnotations` | ❌ | Optional metadata hints about tool behavior |

## Behavior

From the [spec](https://webmachinelearning.github.io/webmcp):

> Registers a single tool without clearing the existing set of tools. The method throws
> an error, if a tool with the same name already exists, or if the `inputSchema` is invalid.

### Step-by-step:

1. Validate that `tool.name` is a non-empty string.
2. Check if a tool with the same `name` already exists in the registered set.
   - If yes, throw a `TypeError`.
3. If `tool.inputSchema` is provided, validate it as a JSON Schema object.
   - If invalid, throw a `TypeError`.
4. Add the tool to the registered set.
5. Notify connected agents that the tool list has been updated.

## Basic Example

```javascript
navigator.modelContext.registerTool({
  name: "add-todo",
  description: "Add a new todo item to the list",
  inputSchema: {
    type: "object",
    properties: {
      text: { type: "string", description: "The text of the todo item" }
    },
    required: ["text"]
  },
  execute: ({ text }, client) => {
    addTodoItem(text);
    return {
      content: [
        { type: "text", text: `Todo "${text}" added successfully.` }
      ]
    };
  }
});
```

## Error Handling — Duplicate Names

```javascript
// First registration succeeds
navigator.modelContext.registerTool({
  name: "search",
  description: "Search products",
  execute: async ({ query }) => { /* ... */ }
});

// Second registration with same name throws
try {
  navigator.modelContext.registerTool({
    name: "search",  // Duplicate!
    description: "Search articles",
    execute: async ({ query }) => { /* ... */ }
  });
} catch (e) {
  console.error(e); // TypeError: A tool named "search" is already registered
}
```

### Updating a Tool

To update a tool's definition, first unregister it, then re-register:

```javascript
// Update by unregister + re-register
navigator.modelContext.unregisterTool("search");
navigator.modelContext.registerTool({
  name: "search",
  description: "Updated search with new parameters",
  inputSchema: { /* updated schema */ },
  execute: async ({ query, filters }) => { /* updated handler */ }
});
```

## Error Handling — Invalid inputSchema

```javascript
try {
  navigator.modelContext.registerTool({
    name: "broken-tool",
    description: "This will fail",
    inputSchema: "not-an-object",  // Invalid! Must be an object
    execute: async () => {}
  });
} catch (e) {
  console.error(e); // TypeError: inputSchema is not a valid JSON Schema
}
```

## Tool Without inputSchema

Tools can omit `inputSchema` entirely if they take no parameters:

```javascript
navigator.modelContext.registerTool({
  name: "get-cart-total",
  description: "Get the current shopping cart total",
  execute: async (input, client) => {
    const total = calculateCartTotal();
    return {
      content: [
        { type: "text", text: `Cart total: $${total.toFixed(2)}` }
      ]
    };
  }
});
```

## Complex Example — E-commerce

From the [API proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):
// Register a purchase tool with user interaction
navigator.modelContext.registerTool({
  name: "buyProduct",
  description: "Use this tool to purchase a product given its unique product_id.",
  inputSchema: {
    type: "object",
    properties: {
      product_id: {
        description: "The unique identifier for the product to be purchased.",
        type: "string",
      }
    },
    required: ["product_id"]
  },
  execute: async function buyProduct({ product_id }, client) {
    // Request user confirmation before executing the action
    const confirmed = await client.requestUserInteraction(async () => {
      return new Promise((resolve) => {
        const ok = confirm(`Buy product ${product_id}?\nClick OK to confirm.`);
        resolve(ok);
      });
    });

    if (!confirmed) {
      throw new Error("Purchase cancelled by user.");
    }

    executePurchase(product_id);
    return {
      content: [
        { type: "text", text: `Product ${product_id} purchased.` }
      ]
    };
  }
});
```

## registerTool vs. provideContext

| Behavior | `registerTool()` | `provideContext()` |
|---|---|---|
| **Clears existing tools** | ❌ No | ✅ Yes |
| **Duplicate names** | Throws error | Expected to be unique within the tools array |
| **Best for** | Adding tools incrementally | Setting the full tool set at once |
| **SPA state changes** | Manual add/remove | Call again with new set |
| **Number of tools** | One at a time | Multiple in one call |

### When to Use registerTool

- **Progressive enhancement** — Add tools as features load
- **Plugin architectures** — Third-party scripts register their own tools
- **Dynamic features** — Add tools in response to user actions
- **Granular control** — Add/remove individual tools without affecting others

```javascript
// Progressive: add tools as modules load
import { searchModule } from "./search.js";
import { cartModule } from "./cart.js";

// Each module registers its own tools
searchModule.init(); // internally calls registerTool("search", ...)
cartModule.init();   // internally calls registerTool("add-to-cart", ...)
```

### When to Use provideContext

- **SPA navigation** — Replace entire tool set when view changes
- **Simple apps** — Register all tools at once on page load
- **State-dependent tools** — Different tools for different UI states

```javascript
// SPA: different tools for different views
function onRouteChange(route) {
  if (route === "/editor") {
    navigator.modelContext.provideContext({
      tools: [editorTools.save, editorTools.undo, editorTools.format]
    });
  } else if (route === "/gallery") {
    navigator.modelContext.provideContext({
      tools: [galleryTools.search, galleryTools.filter, galleryTools.sort]
    });
  }
}
```

## Batch Registration Pattern

To register multiple tools without using `provideContext()`, loop over an array:

```javascript
const tools = [
  { name: "add-stamp", description: "Add a new stamp", /* ... */ },
  { name: "get-stamps", description: "List all stamps", /* ... */ },
  { name: "update-stamp", description: "Update stamp details", /* ... */ },
];

for (const tool of tools) {
  navigator.modelContext.registerTool(tool);
}
```

> **Note:** Unlike `provideContext()`, this does not clear existing tools first.

The API is available in [Chrome's Early Preview](https://developer.chrome.com/blog/webmcp-epp)
([join EPP](https://developer.chrome.com/docs/ai/join-epp)).

## Related Issues

- [Issue #15 — Dual registration](https://github.com/webmachinelearning/webmcp/issues/15) — registerTool vs provideContext design
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — HTML-based tool definitions as alternative
- [Issue #24 — Bikeshedding the global name](https://github.com/webmachinelearning/webmcp/issues/24) — How the API surface was named

## See Also

- [provideContext() method](provide-context.md)
- [Tool registration interface](tool-registration-interface.md)
- [Execute callback specification](execute-callback.md)
- [unregisterTool() and clearContext()](unregister-tool.md)
- [inputSchema for tool inputs](input-schema.md)
- [outputSchema for structured outputs](output-schema.md)
- [ToolAnnotations metadata](annotations.md)
- [navigator.modelContext API](navigator-model-context.md)
- [Imperative vs declarative](../03-declarative-api/imperative-vs-declarative.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Imperative API tutorial](../12-tutorials/imperative-api-tutorial.md)
- [Tool poisoning attacks](../04-security/tool-poisoning.md)
