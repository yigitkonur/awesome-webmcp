# Imperative API Tutorial

> A step-by-step guide to the WebMCP imperative API — registering tools with JavaScript, defining input schemas, writing execute callbacks, returning structured content, and handling errors.

---

## Overview

The imperative API gives you full programmatic control over WebMCP tools. You write JavaScript to define tool schemas and implement their behavior. This is the recommended approach for:

- Dynamic tools that change based on application state
- Complex operations requiring async processing
- Tools that need fine-grained error handling
- Integration with existing JavaScript application code

---

## API Methods

The `navigator.modelContext` interface provides four methods:

| Method | Description |
|--------|-------------|
| `provideContext({ tools })` | Register a full set of tools (replaces all existing tools) |
| `registerTool(descriptor)` | Add a single tool without affecting others |
| `unregisterTool(name)` | Remove a single tool by name |
| `clearContext()` | Remove all registered tools |

---

## provideContext vs registerTool

### Use `provideContext` When:

- **Setting up all tools at once** — e.g., on page load
- **Replacing the entire tool set** — e.g., when navigating between SPA views
- **State-dependent tools** — different tools for different UI states

```js
// On page load: register all tools
navigator.modelContext.provideContext({
  tools: [searchTool, filterTool, sortTool]
});

// When user opens a product: replace with product-specific tools
navigator.modelContext.provideContext({
  tools: [addToCartTool, getDetailsTool, compareTooln]
});
```

> **Important**: Each call to `provideContext` clears all previously registered tools. Tool names within a single call must be unique.

### Use `registerTool` / `unregisterTool` When:

- **Adding tools incrementally** — without disturbing existing tools
- **Conditionally available tools** — e.g., a tool that appears only after login
- **Third-party scripts** — multiple scripts each registering their own tools

```js
// Base tools always available
navigator.modelContext.registerTool(searchTool);
navigator.modelContext.registerTool(viewTool);

// Conditionally add checkout tool after login
if (user.isAuthenticated) {
  navigator.modelContext.registerTool(checkoutTool);
}

// Remove tool when no longer relevant
navigator.modelContext.unregisterTool("checkout");
```

---

## Defining inputSchema

The `inputSchema` follows [JSON Schema](https://json-schema.org/) format, aligned with MCP tool definitions:

### Basic Types

```js
{
  type: "object",
  properties: {
    // String parameter
    name: {
      type: "string",
      description: "The user's full name"
    },
    // Number with constraints
    quantity: {
      type: "number",
      description: "Number of items",
      minimum: 1,
      maximum: 100
    },
    // Enum (fixed options)
    status: {
      type: "string",
      enum: ["active", "inactive", "pending"],
      description: "Account status"
    },
    // Boolean
    urgent: {
      type: "boolean",
      description: "Whether this is urgent"
    },
    // Array
    tags: {
      type: "array",
      items: { type: "string" },
      description: "List of tags to apply"
    }
  },
  required: ["name", "quantity"]
}
```

### Nested Objects

```js
{
  type: "object",
  properties: {
    shipping_address: {
      type: "object",
      description: "Delivery address",
      properties: {
        street: { type: "string", description: "Street address" },
        city: { type: "string", description: "City name" },
        state: { type: "string", description: "State/province" },
        zip: { type: "string", description: "Postal code" },
        country: { type: "string", description: "Country code (e.g., US, CA)" }
      },
      required: ["street", "city", "zip"]
    }
  }
}
```

### Schema Design Tips

| Tip | Example |
|-----|---------|
| **Accept strings for dates** | `"2026-03-15"` not epoch timestamps |
| **Use enums for known values** | `enum: ["economy", "business", "first"]` |
| **Describe constraints in text** | `"Username (3-20 chars, alphanumeric)"` |
| **Mark truly required fields** | Only what's needed for the operation to work |
| **Keep schemas flat when possible** | Avoid deep nesting |

---

## Writing Execute Callbacks

The `execute` function receives two arguments:

1. **params** — The validated input parameters (destructured from the inputSchema)
2. **agent** — The agent interface (provides `requestUserInteraction`)

### Synchronous Execution

```js
execute({ name, email }) {
  // Perform the action
  const user = createUser(name, email);
  
  // Update the UI
  renderUserList();
  
  // Return result to agent
  return {
    content: [{
      type: "text",
      text: `User "${name}" created with ID: ${user.id}`
    }]
  };
}
```

### Async Execution

```js
async execute({ query }) {
  // Async operations work naturally
  const results = await api.search(query);
  
  renderSearchResults(results);
  
  return {
    content: [{
      type: "text",
      text: JSON.stringify({
        total: results.length,
        items: results.slice(0, 10)
      })
    }]
  };
}
```

### Using the Agent Interface

The `agent` parameter provides `requestUserInteraction` for requiring human confirmation:

```js
async execute({ product_id }, agent) {
  const product = await getProduct(product_id);

  // Request user interaction — pauses tool execution until user responds
  const confirmed = await agent.requestUserInteraction(async () => {
    // This callback runs in the page context
    // Show a confirmation dialog, payment form, etc.
    return new Promise((resolve) => {
      const result = confirm(`Purchase "${product.name}" for ${product.price}?`);
      resolve(result);
    });
  });

  if (!confirmed) {
    throw new Error("Purchase cancelled by user.");
  }

  const order = await purchaseProduct(product_id);
  return {
    content: [{
      type: "text",
      text: `Order #${order.id} placed for "${product.name}".`
    }]
  };
}
```

### Multiple User Interactions

You can call `requestUserInteraction` multiple times during a single tool execution:

```js
async execute({ items }, agent) {
  // First interaction: confirm items
  const itemsConfirmed = await agent.requestUserInteraction(async () => {
    showItemReview(items);
    return waitForConfirmation();
  });
  if (!itemsConfirmed) throw new Error("Cancelled.");

  // Second interaction: select shipping
  const shippingChoice = await agent.requestUserInteraction(async () => {
    showShippingOptions();
    return waitForShippingSelection();
  });

  // Third interaction: payment
  const paymentResult = await agent.requestUserInteraction(async () => {
    showPaymentForm();
    return waitForPayment();
  });

  return {
    content: [{
      type: "text",
      text: `Order placed with ${shippingChoice} shipping.`
    }]
  };
}
```

---

## Returning Content Arrays

The `execute` function returns an object with a `content` array. Each item has a `type` field:

### Text Response

```js
return {
  content: [{
    type: "text",
    text: "Operation completed successfully."
  }]
};
```

### Structured Data (as JSON string)

```js
return {
  content: [{
    type: "text",
    text: JSON.stringify({
      results: [
        { id: 1, name: "Item A", price: 29.99 },
        { id: 2, name: "Item B", price: 49.99 }
      ],
      total: 2,
      page: 1
    })
  }]
};
```

### Multiple Content Items

```js
return {
  content: [
    {
      type: "text",
      text: "Found 3 matching products:"
    },
    {
      type: "text",
      text: JSON.stringify(products)
    }
  ]
};
```

---

## Handling Errors

### Throwing Errors

The simplest error handling — throw an Error with a descriptive message:

```js
execute({ product_id }) {
  const product = getProduct(product_id);
  
  if (!product) {
    throw new Error(`Product "${product_id}" not found. Check the product ID and try again.`);
  }
  
  if (!product.inStock) {
    throw new Error(`Product "${product.name}" is currently out of stock.`);
  }
  
  if (product.price > 10000) {
    throw new Error(`Product "${product.name}" exceeds the maximum order value. Please contact sales.`);
  }

  // ... proceed with operation
}
```

### Descriptive Error Messages

Write errors that help the agent self-correct:

```js
// ❌ Bad: Generic error
throw new Error("Invalid input");

// ✅ Good: Actionable error with correct format
throw new Error('Invalid date format "March 15". Please use YYYY-MM-DD format (e.g., "2026-03-15").');

// ❌ Bad: Vague failure
throw new Error("Operation failed");

// ✅ Good: Specific with retry guidance  
throw new Error('Flight search failed: no flights found for SFO → XYZ. "XYZ" is not a valid airport code. Try a valid IATA code like "JFK" or "LAX".');
```

### Validation in Execute (Not Just Schema)

The inputSchema provides basic type checking, but always validate in code too:

```js
execute({ email, age }) {
  // Schema says type: "string" but doesn't validate email format
  if (!email.includes('@')) {
    throw new Error(`"${email}" is not a valid email address.`);
  }

  // Schema says type: "number" but doesn't check business rules
  if (age < 18) {
    throw new Error("User must be 18 or older to create an account.");
  }

  // Proceed with validated inputs
  // ...
}
```

---

## Complete Example: Notes Application

A full imperative API implementation for a notes app:

```js
const notes = [];

if ("modelContext" in navigator) {
  navigator.modelContext.provideContext({
    tools: [
      {
        name: "create-note",
        description: "Create a new note with a title and content.",
        inputSchema: {
          type: "object",
          properties: {
            title: { type: "string", description: "Note title" },
            content: { type: "string", description: "Note content (supports markdown)" },
            tags: {
              type: "array",
              items: { type: "string" },
              description: "Tags for categorization"
            }
          },
          required: ["title", "content"]
        },
        execute({ title, content, tags = [] }) {
          const note = {
            id: crypto.randomUUID(),
            title,
            content,
            tags,
            created: new Date().toISOString(),
            updated: new Date().toISOString()
          };
          notes.push(note);
          renderNotes();

          return {
            content: [{
              type: "text",
              text: `Note "${title}" created (ID: ${note.id})`
            }]
          };
        }
      },
      {
        name: "search-notes",
        description: "Search notes by keyword or tag.",
        inputSchema: {
          type: "object",
          properties: {
            query: { type: "string", description: "Search keyword" },
            tag: { type: "string", description: "Filter by tag" }
          }
        },
        execute({ query, tag }) {
          let results = notes;
          if (query) {
            results = results.filter(n =>
              n.title.toLowerCase().includes(query.toLowerCase()) ||
              n.content.toLowerCase().includes(query.toLowerCase())
            );
          }
          if (tag) {
            results = results.filter(n => n.tags.includes(tag));
          }

          return {
            content: [{
              type: "text",
              text: JSON.stringify(results.map(n => ({
                id: n.id, title: n.title, tags: n.tags,
                preview: n.content.slice(0, 100)
              })))
            }]
          };
        }
      },
      {
        name: "update-note",
        description: "Update an existing note's title, content, or tags.",
        inputSchema: {
          type: "object",
          properties: {
            id: { type: "string", description: "Note ID to update" },
            title: { type: "string", description: "New title (omit to keep current)" },
            content: { type: "string", description: "New content (omit to keep current)" },
            tags: { type: "array", items: { type: "string" }, description: "New tags (replaces existing)" }
          },
          required: ["id"]
        },
        execute({ id, title, content, tags }) {
          const note = notes.find(n => n.id === id);
          if (!note) throw new Error(`Note ${id} not found.`);

          if (title) note.title = title;
          if (content) note.content = content;
          if (tags) note.tags = tags;
          note.updated = new Date().toISOString();

          renderNotes();

          return {
            content: [{
              type: "text",
              text: `Note "${note.title}" updated.`
            }]
          };
        }
      },
      {
        name: "delete-note",
        description: "Permanently delete a note.",
        inputSchema: {
          type: "object",
          properties: {
            id: { type: "string", description: "Note ID to delete" }
          },
          required: ["id"]
        },
        execute({ id }) {
          const index = notes.findIndex(n => n.id === id);
          if (index === -1) throw new Error(`Note ${id} not found.`);

          const deleted = notes.splice(index, 1)[0];
          renderNotes();

          return {
            content: [{
              type: "text",
              text: `Note "${deleted.title}" deleted.`
            }]
          };
        }
      }
    ]
  });
}
```

---

## Tool Execution Flow

```
Agent decides to call tool "create-note"
  ↓
Browser validates parameters against inputSchema
  ↓
Browser calls execute({ title, content, tags }, agent)
  ↓
execute() runs your JavaScript code
  ↓
execute() returns { content: [...] }
  ↓
Browser sends content back to agent
  ↓
Agent processes result and continues
```

Key details:
- Tools execute **sequentially** — one at a time on the main thread
- The page can update UI during execution
- Async tools (returning Promises) are awaited before the next tool call
- If `execute` throws, the error message is sent to the agent

---

## See Also

- [Declarative API Tutorial](./declarative-api-tutorial.md) — Form-based tools without JavaScript
- [Getting Started](./getting-started.md) — Quick start guide
- [Best Practices](./best-practices.md) — Tool design guidelines
- [Testing Tools](./testing-tools.md) — Testing and debugging
- [Developer Tooling Use Case](../11-use-cases/developer-tooling.md) — Stamp database example
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../02-specification/register-tool.md) — Tool registration API
- [Execute callback](../02-specification/execute-callback.md) — Execute callback
- [Input schema specification](../02-specification/input-schema.md) — Input schema specification
- [Output schema specification](../02-specification/output-schema.md) — Output schema specification
- [Abort signal handling](../02-specification/abort-signal.md) — Abort signal handling
- [Travel demo example](../08-ecosystem/demos/travel-demo.md) — Travel demo example
- [Security checklist](../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../15-reference/api-reference.md) — Complete API reference
- [JSON schema patterns](../15-reference/json-schema-patterns.md) — JSON schema patterns
