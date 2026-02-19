# ToolExecuteCallback — The Tool Implementation Function

## Overview

The `ToolExecuteCallback` is the JavaScript function that implements a tool's behavior.
When an agent invokes a tool, the browser calls this function with the agent's input
parameters and a `ModelContextClient` object (see [spec](https://webmachinelearning.github.io/webmcp)). The function can be synchronous or
asynchronous and returns a result that is sent back to the agent.

## WebIDL Definition

```webidl
callback ToolExecuteCallback = Promise<any> (object input, ModelContextClient client);
```

## Function Signature

```javascript
async function toolHandler(input, client) {
  // input: object matching the tool's inputSchema
  // client: ModelContextClient instance representing the calling agent
  // returns: any (typically a content array object)
}
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `input` | `object` | Input parameters provided by the agent, validated against `inputSchema` |
| `client` | `ModelContextClient` | Interface to the calling agent, provides `requestUserInteraction()` |

### Return Value

The callback returns `Promise<any>`. By convention, the return value should follow the
MCP content array pattern:

```javascript
return {
  content: [
    { type: "text", text: "Result message or JSON data" }
  ]
};
```

## The input Parameter

The `input` object contains the parameters the agent provided when calling the tool.
These match the shape defined in `inputSchema`:

```javascript
navigator.modelContext.registerTool({
  name: "add-stamp",
  description: "Add a new stamp to the collection",
  inputSchema: {
    type: "object",
    properties: {
      name: { type: "string", description: "Stamp name" },
      year: { type: "number", description: "Issue year" },
      imageUrl: { type: "string", description: "Optional image URL" }
    },
    required: ["name", "year"]
  },
  execute: async ({ name, year, imageUrl }) => {
    // name: string (required — always present)
    // year: number (required — always present)
    // imageUrl: string | undefined (optional — may not be provided)

    stamps.push({ name, year, imageUrl: imageUrl || null });
    renderStamps();

    return {
      content: [{
        type: "text",
        text: `Stamp "${name}" (${year}) added.`
      }]
    };
  }
});
```

### Destructuring Pattern

The most common pattern is to destructure the `input` object directly in the
function signature:

```javascript
// Arrow function with destructuring
execute: async ({ query, maxResults = 10 }) => { /* ... */ }

// Named function with destructuring
execute: async function searchProducts({ query, maxResults = 10 }, client) { /* ... */ }

// Without destructuring
execute: async (input, client) => {
  const query = input.query;
  const maxResults = input.maxResults || 10;
}
```

### Tools Without Parameters

When `inputSchema` is omitted, the `input` parameter is an empty object or undefined:

```javascript
execute: async () => {
  // No input parameters — input is {} or undefined
  return {
    content: [{ type: "text", text: new Date().toISOString() }]
  };
}
```

## The client Parameter (ModelContextClient)

The `client` parameter is a `ModelContextClient` instance representing the AI agent
that invoked the tool. Its lifetime is scoped to the tool execution.

```webidl
[Exposed=Window, SecureContext]
interface ModelContextClient {
  Promise<any> requestUserInteraction(UserInteractionCallback callback);
};

callback UserInteractionCallback = Promise<any> ();
```

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> The `agent` interface is introduced to represent an AI Agent using the functionality
> declared by the site through the `modelContext`. The lifetime of this interface is
> scoped to the execution of a tool.

### requestUserInteraction()

The primary method on `ModelContextClient`. It asynchronously requests user input
during tool execution:

```javascript
execute: async function buyProduct({ product_id }, client) {
  // Request user confirmation
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
    content: [{ type: "text", text: `Product ${product_id} purchased.` }]
  };
}
```

### Multiple User Interactions

`requestUserInteraction` can be called multiple times during a single tool execution:

```javascript
execute: async function transferFunds({ from, to, amount }, client) {
  // First interaction: confirm the transfer
  const confirmed = await client.requestUserInteraction(async () => {
    return confirm(`Transfer $${amount} from ${from} to ${to}?`);
  });
  if (!confirmed) throw new Error("Transfer cancelled");

  // Second interaction: enter 2FA code
  const code = await client.requestUserInteraction(async () => {
    return prompt("Enter your 2FA verification code:");
  });
  if (!code) throw new Error("2FA not provided");

  await processTransfer(from, to, amount, code);
  return {
    content: [{ type: "text", text: `$${amount} transferred from ${from} to ${to}` }]
  };
}
```

## Async / Sync Patterns

### Synchronous Execution

Simple tools can return values directly (auto-wrapped in a resolved promise):

```javascript
execute: ({ x, y }) => {
  return {
    content: [{
      type: "text",
      text: `${x} + ${y} = ${x + y}`
    }]
  };
}
```

### Asynchronous Execution

Tools that perform I/O, network requests, or user interactions should be async:

```javascript
execute: async ({ query }) => {
  const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`);
  const results = await response.json();

  return {
    content: [{
      type: "text",
      text: JSON.stringify(results)
    }]
  };
}
```

### Worker Delegation

For computationally heavy operations, delegate to a Web Worker:

```javascript
execute: async ({ dataset }) => {
  return new Promise((resolve, reject) => {
    const worker = new Worker("analysis-worker.js");

    worker.onmessage = (e) => {
      resolve({
        content: [{ type: "text", text: JSON.stringify(e.data.result) }]
      });
      worker.terminate();
    };

    worker.onerror = (e) => {
      reject(new Error(`Analysis failed: ${e.message}`));
      worker.terminate();
    };

    worker.postMessage({ dataset });
  });
}
```

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> Handling tool calls in the main thread with the option of delegating to workers
> serves a few purposes:
> - Ensures tool calls run one at a time and sequentially.
> - The page can update UI to reflect state changes performed by tools.
> - Handling tool calls in page script may be sufficient for simple applications.

## Return Value Format

### Content Array (Recommended)

```javascript
return {
  content: [
    { type: "text", text: "Human-readable message" },
    { type: "text", text: JSON.stringify(structuredData) }
  ]
};
```

### Simple Return (Also Valid)

```javascript
// Any return value is accepted
return "Simple string result";
return { status: "ok", count: 42 };
return [1, 2, 3];
```

However, the content array format is preferred for [MCP](https://modelcontextprotocol.io/introduction) compatibility and consistency.

## Error Handling

### Throwing Errors

Thrown errors are caught by the browser and communicated to the agent:

```javascript
execute: async ({ productId }) => {
  const product = await getProduct(productId);
  if (!product) {
    throw new Error(`Product ${productId} not found`);
  }
  if (!product.inStock) {
    throw new Error(`Product ${productId} is out of stock`);
  }
  return {
    content: [{ type: "text", text: JSON.stringify(product) }]
  };
}
```

### Promise Rejection

Rejected promises behave the same as thrown errors:

```javascript
execute: ({ id }) => {
  return fetch(`/api/items/${id}`)
    .then(r => {
      if (!r.ok) return Promise.reject(new Error(`HTTP ${r.status}`));
      return r.json();
    })
    .then(data => ({
      content: [{ type: "text", text: JSON.stringify(data) }]
    }));
}
```

### User Cancellation

```javascript
execute: async ({ action }, client) => {
  const confirmed = await client.requestUserInteraction(async () => {
    return confirm(`Perform ${action}?`);
  });

  if (!confirmed) {
    // Throwing an error signals cancellation to the agent
    throw new Error(`User cancelled: ${action}`);
  }

  // Proceed with action...
}
```

## UI Updates During Execution

Since tool callbacks run on the main thread, they can directly update the DOM:

```javascript
execute: async ({ name, description, year, imageUrl }) => {
  // 1. Update data model
  stamps.push({ name, description, year, imageUrl: imageUrl || null });

  // 2. Update UI — runs on main thread, so DOM access is safe
  document.getElementById("confirmationMessage").textContent =
    `Stamp "${name}" added successfully!`;
  renderStamps();

  // 3. Return result to agent
  return {
    content: [{
      type: "text",
      text: `Stamp "${name}" added. Collection now has ${stamps.length} stamps.`
    }]
  };
}
```

## Complete Example: Stamp Database Tool

From the [spec proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

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

## Related Issues

- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Return value structure
- [Issue #48 — Abort callback](https://github.com/webmachinelearning/webmcp/issues/48) — Cancellation during execution ([AbortSignal](abort-signal.md))
- [Issue #21 — Elicitation / user interaction](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction design

## See Also

- [registerTool() method](register-tool.md)
- [Tool registration interface](tool-registration-interface.md)
- [AbortSignal for cancellation](abort-signal.md)
- [inputSchema for tool inputs](input-schema.md)
- [outputSchema for structured outputs](output-schema.md)
- [requestUserInteraction() API](../06-user-interaction/request-user-interaction.md)
- [Human-in-the-loop philosophy](../06-user-interaction/human-in-the-loop.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [Concurrent execution](../07-architecture/concurrent-execution.md)
- [Imperative API tutorial](../12-tutorials/imperative-api-tutorial.md)
