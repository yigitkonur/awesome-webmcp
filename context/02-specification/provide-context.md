# provideContext() — Register Tools with Full Replacement

## Overview

`provideContext()` registers a set of tools with the browser, **clearing any pre-existing
tools and other context** before registering the new ones. This is the primary method for
declaring the full set of tools a page makes available to AI agents. It is part of the
[dual registration API](https://github.com/webmachinelearning/webmcp/issues/15) defined
in the [spec](https://webmachinelearning.github.io/webmcp).

## Signature

```webidl
[Exposed=Window, SecureContext]
interface ModelContext {
  undefined provideContext(optional ModelContextOptions options = {});
};
```

### Parameters

| Parameter | Type | Required | Default | Description |
|---|---|---|---|---|
| `options` | `ModelContextOptions` | No | `{}` | Dictionary containing the tools to register |

### ModelContextOptions Dictionary

```webidl
dictionary ModelContextOptions {
  sequence<ModelContextTool> tools = [];
};
```

| Field | Type | Default | Description |
|---|---|---|---|
| `tools` | `sequence<ModelContextTool>` | `[]` | List of tools to register. Each tool name must be unique. |

### Return Value

`undefined` — The method does not return a value.

## Behavior

From the [spec](https://webmachinelearning.github.io/webmcp):

> Registers the provided context (tools) with the browser. This method clears any
> pre-existing tools and other context before registering the new ones.

### Step-by-step:

1. Clear all previously registered tools from the model context.
2. For each tool in `options.tools`:
   a. Validate the tool conforms to the `ModelContextTool` dictionary.
   b. Ensure the tool name is unique within the provided list.
   c. If `inputSchema` is present, validate it as a JSON Schema object.
   d. Register the tool.
3. Notify connected agents that the tool list has been updated.

### Clear-then-set Semantics

The critical distinction of `provideContext()` is that it **replaces** rather than
**appends**. Every call wipes the slate clean:

```javascript
// First call: registers "search" and "filter"
navigator.modelContext.provideContext({
  tools: [
    { name: "search", description: "Search items", execute: searchFn },
    { name: "filter", description: "Filter results", execute: filterFn }
  ]
});

// Second call: "search" and "filter" are REMOVED, only "sort" exists now
navigator.modelContext.provideContext({
  tools: [
    { name: "sort", description: "Sort results", execute: sortFn }
  ]
});
```

### Calling with No Arguments

Calling `provideContext()` without arguments (or with an empty tools array) is
equivalent to calling `clearContext()`:

```javascript
// These are equivalent:
navigator.modelContext.provideContext();
navigator.modelContext.provideContext({});
navigator.modelContext.provideContext({ tools: [] });
navigator.modelContext.clearContext();
```

## Basic Example

```javascript
if ("modelContext" in navigator) {
  navigator.modelContext.provideContext({
    tools: [
      {
        name: "add-stamp",
        description: "Add a new stamp to the collection",
        inputSchema: {
          type: "object",
          properties: {
            name: { type: "string", description: "The name of the stamp" },
            description: { type: "string", description: "A brief description" },
            year: { type: "number", description: "Year the stamp was issued" },
            imageUrl: { type: "string", description: "Optional image URL" }
          },
          required: ["name", "description", "year"]
        },
        execute({ name, description, year, imageUrl }, client) {
          addStamp(name, description, year, imageUrl);
          return {
            content: [{
              type: "text",
              text: `Stamp "${name}" added successfully! ` +
                    `The collection now contains ${stamps.length} stamps.`
            }]
          };
        }
      }
    ]
  });
}
```

## SPA State Management

The clear-then-set semantics make `provideContext()` ideal for single-page applications
where the available tools change based on UI state. This is available in
[Chrome's Early Preview](https://developer.chrome.com/blog/webmcp-epp).

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> The `provideContext` method can be called multiple times. Subsequent calls clear any
> pre-existing tools and other context before registering the new ones. This is useful
> for single-page web apps that frequently change UI state and could benefit from
> presenting different tools depending on which state the UI is currently in.

### Example: Route-Based Tool Switching

```javascript
const router = {
  "/inbox": () => {
    navigator.modelContext.provideContext({
      tools: [
        {
          name: "search-emails",
          description: "Search emails by sender, subject, or content",
          inputSchema: {
            type: "object",
            properties: {
              query: { type: "string", description: "Search query" },
              folder: { type: "string", enum: ["inbox", "sent", "drafts"] }
            },
            required: ["query"]
          },
          execute: searchEmails
        },
        {
          name: "compose-email",
          description: "Open a new email composition form",
          inputSchema: {
            type: "object",
            properties: {
              to: { type: "string", description: "Recipient email address" },
              subject: { type: "string", description: "Email subject" }
            }
          },
          execute: composeEmail
        }
      ]
    });
  },

  "/calendar": () => {
    navigator.modelContext.provideContext({
      tools: [
        {
          name: "create-event",
          description: "Create a new calendar event",
          inputSchema: {
            type: "object",
            properties: {
              title: { type: "string" },
              date: { type: "string", format: "date" },
              time: { type: "string", format: "time" }
            },
            required: ["title", "date"]
          },
          execute: createEvent
        }
      ]
    });
  }
};

// On SPA route change
window.addEventListener("popstate", () => {
  const handler = router[location.pathname];
  if (handler) handler();
});
```

### Example: Conditional Tools Based on Auth State

```javascript
function updateToolContext() {
  const tools = [
    {
      name: "browse-products",
      description: "Browse the product catalog",
      execute: browseProducts
    }
  ];

  if (user.isAuthenticated) {
    tools.push(
      {
        name: "add-to-cart",
        description: "Add a product to your shopping cart",
        inputSchema: {
          type: "object",
          properties: {
            productId: { type: "string" },
            quantity: { type: "integer", minimum: 1 }
          },
          required: ["productId"]
        },
        execute: addToCart
      },
      {
        name: "checkout",
        description: "Proceed to checkout with current cart",
        execute: checkout
      }
    );
  }

  navigator.modelContext.provideContext({ tools });
}

// Re-register tools when auth state changes
auth.onStateChange(() => updateToolContext());
```

## Comparison with registerTool

| Aspect | `provideContext()` | `registerTool()` |
|---|---|---|
| **Clears existing tools** | ✅ Yes — full replacement | ❌ No — additive |
| **Number of tools** | Multiple (array) | Single |
| **Duplicate name handling** | Names must be unique within array | Throws on duplicate with existing set |
| **Best use case** | SPA state changes, full declarations | Incremental additions, plugins |
| **Call frequency** | On each state change | On each feature load |
| **Mental model** | "Here is my complete tool set" | "Add this one tool" |

### Decision Guide

```
Is this a single-page application with view changes?
  → Use provideContext() on each navigation

Are tools loaded from separate modules/plugins?
  → Use registerTool() in each module

Do you need to atomically replace the entire tool set?
  → Use provideContext()

Do you need to add/remove individual tools?
  → Use registerTool() / unregisterTool()
```

## Multiple Tools Example

```javascript
navigator.modelContext.provideContext({
  tools: [
    {
      name: "get-stamps",
      description: "Retrieve all stamps in the collection",
      annotations: { readOnlyHint: true },
      execute: async () => ({
        content: [{ type: "text", text: JSON.stringify(stamps) }]
      })
    },
    {
      name: "add-stamp",
      description: "Add a new stamp to the collection",
      inputSchema: {
        type: "object",
        properties: {
          name: { type: "string", description: "Stamp name" },
          year: { type: "number", description: "Issue year" }
        },
        required: ["name", "year"]
      },
      execute: ({ name, year }) => {
        stamps.push({ name, year });
        renderStamps();
        return {
          content: [{ type: "text", text: `Added "${name}" (${year})` }]
        };
      }
    },
    {
      name: "delete-stamp",
      description: "Remove a stamp by name",
      inputSchema: {
        type: "object",
        properties: {
          name: { type: "string", description: "Name of the stamp to remove" }
        },
        required: ["name"]
      },
      execute: ({ name }) => {
        const idx = stamps.findIndex(s => s.name === name);
        if (idx === -1) throw new Error(`Stamp "${name}" not found`);
        stamps.splice(idx, 1);
        renderStamps();
        return {
          content: [{ type: "text", text: `Stamp "${name}" removed` }]
        };
      }
    }
  ]
});
```

## Related Issues

- [Issue #15 — Dual registration](https://github.com/webmachinelearning/webmcp/issues/15) — provideContext vs registerTool design
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — HTML-based alternative
- [Issue #24 — Bikeshedding the global name](https://github.com/webmachinelearning/webmcp/issues/24) — API naming

## See Also

- [Specification overview](spec-overview.md)
- [registerTool() method](register-tool.md)
- [unregisterTool() and clearContext()](unregister-tool.md)
- [Tool registration interface](tool-registration-interface.md)
- [Execute callback specification](execute-callback.md)
- [navigator.modelContext API](navigator-model-context.md)
- [inputSchema for tool inputs](input-schema.md)
- [ToolAnnotations metadata](annotations.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Imperative vs declarative](../03-declarative-api/imperative-vs-declarative.md)
- [Concurrent execution](../07-architecture/concurrent-execution.md)
