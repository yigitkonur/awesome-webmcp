# AbortSignal for Tool Cancellation

## Overview

Tool invocations in WebMCP are asynchronous — they may involve network requests, user
interactions, or long-running computations. When a user cancels a request or an agent
decides to abort, the tool callback needs a mechanism to be informed about the
cancellation. This was discussed in
[Issue #48 — Add a callback to inform the site when tool invocation should be aborted](https://github.com/webmachinelearning/webmcp/issues/48),
opened by [Khushal Sagar](https://github.com/webmachinelearning/webmcp/pull/75) (Google) in November 2025 and resolved in January 2026.

## Problem Statement

From Issue #48:

> Since tool invocation is async, there will be cases when the user cancels the request
> which initiated the execution. We should likely have a callback which informs the site
> about this.

### Scenarios Where Cancellation Occurs

1. **User cancels in agent UI** — User clicks "Stop" or "Cancel" in the agent's chat interface
2. **Agent timeout** — Agent decides the tool is taking too long
3. **Navigation** — User navigates away from the agent conversation
4. **New request supersedes** — User issues a new command that makes the current tool call irrelevant
5. **Agent error recovery** — Agent encounters an error and needs to abort in-flight tool calls

### One-Way Cancellation

From Issue #48:

> I don't think the other way around is necessary. If the site decides to abort, it can
> indicate so in the output of the execution.

Cancellation flows from agent → tool, not tool → agent. If a tool needs to abort itself,
it simply throws an error or returns an error response.

## AbortSignal Integration

The web platform already has a standard cancellation mechanism: the
[AbortController / AbortSignal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal)
API. WebMCP integrates with this pattern by providing an `AbortSignal` to tool callbacks,
similar to how [NavigateEvent](https://developer.mozilla.org/en-US/docs/Web/API/NavigateEvent/signal) provides signals in the Navigation API.

### Proposed Mechanism

The `ModelContextClient` parameter (defined in the [spec](https://webmachinelearning.github.io/webmcp), see also [PR #75](https://github.com/webmachinelearning/webmcp/pull/75)) provides access to
an `AbortSignal` that fires when the agent/user cancels the tool invocation:

```javascript
// Option A: AbortSignal on the client object
execute: async function(input, client) {
  const signal = client.signal; // AbortSignal

  // Use signal with fetch
  const response = await fetch("/api/data", { signal });
  return { content: [{ type: "text", text: await response.text() }] };
}

// Option B: AbortSignal as third parameter (alternative design)
execute: async function(input, client, signal) {
  const response = await fetch("/api/data", { signal });
  return { content: [{ type: "text", text: await response.text() }] };
}
```

## Cancellation Patterns

### Basic: Fetch with AbortSignal

The most common pattern — pass the signal to `fetch()`:

```javascript
navigator.modelContext.registerTool({
  name: "search-products",
  description: "Search the product catalog",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search query" }
    },
    required: ["query"]
  },
  execute: async ({ query }, client) => {
    try {
      const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
        signal: client.signal
      });
      const results = await response.json();
      return {
        content: [{ type: "text", text: JSON.stringify(results) }]
      };
    } catch (e) {
      if (e.name === "AbortError") {
        // Cancellation — clean up and return
        console.log("Search cancelled by user/agent");
        return { content: [{ type: "text", text: "Search cancelled." }] };
      }
      throw e; // Re-throw other errors
    }
  }
});
```

### Polling with Abort Check

For operations that don't natively support AbortSignal:

```javascript
execute: async ({ taskId }, client) => {
  // Poll for completion, checking for abort
  while (true) {
    // Check if cancelled
    if (client.signal.aborted) {
      return { content: [{ type: "text", text: "Operation cancelled." }] };
    }

    const status = await checkTaskStatus(taskId);
    if (status.complete) {
      return {
        content: [{ type: "text", text: JSON.stringify(status.result) }]
      };
    }

    // Wait before polling again
    await new Promise((resolve, reject) => {
      const timeout = setTimeout(resolve, 1000);
      client.signal.addEventListener("abort", () => {
        clearTimeout(timeout);
        reject(new DOMException("Aborted", "AbortError"));
      }, { once: true });
    });
  }
}
```

### Worker with Abort

Terminate a worker when the tool is cancelled:

```javascript
execute: async ({ data }, client) => {
  const worker = new Worker("heavy-computation.js");

  return new Promise((resolve, reject) => {
    // Listen for abort
    client.signal.addEventListener("abort", () => {
      worker.terminate();
      reject(new DOMException("Computation cancelled", "AbortError"));
    }, { once: true });

    worker.onmessage = (e) => {
      resolve({
        content: [{ type: "text", text: JSON.stringify(e.data) }]
      });
      worker.terminate();
    };

    worker.onerror = (e) => {
      reject(new Error(`Worker error: ${e.message}`));
      worker.terminate();
    };

    worker.postMessage({ data });
  });
}
```

### User Interaction with Abort

Cancel a user interaction dialog if the agent aborts:

```javascript
execute: async ({ action }, client) => {
  try {
    const confirmed = await client.requestUserInteraction(async () => {
      // This dialog should be dismissed if cancelled
      return new Promise((resolve, reject) => {
        const dialog = document.getElementById("confirmation-dialog");
        dialog.showModal();

        client.signal.addEventListener("abort", () => {
          dialog.close();
          reject(new DOMException("Cancelled", "AbortError"));
        }, { once: true });

        dialog.querySelector(".confirm").onclick = () => {
          dialog.close();
          resolve(true);
        };
        dialog.querySelector(".cancel").onclick = () => {
          dialog.close();
          resolve(false);
        };
      });
    });

    if (!confirmed) throw new Error("User declined");
    await performAction(action);
    return { content: [{ type: "text", text: `${action} completed.` }] };
  } catch (e) {
    if (e.name === "AbortError") {
      return { content: [{ type: "text", text: "Operation cancelled." }] };
    }
    throw e;
  }
}
```

## Cleanup on Abort

When a tool is cancelled, the callback should clean up any resources:

```javascript
execute: async ({ batchItems }, client) => {
  const processedItems = [];
  const rollbackActions = [];

  try {
    for (const item of batchItems) {
      // Check abort before each item
      if (client.signal.aborted) {
        throw new DOMException("Aborted", "AbortError");
      }

      await processItem(item);
      processedItems.push(item);
      rollbackActions.push(() => undoProcessItem(item));
    }

    return {
      content: [{
        type: "text",
        text: `Processed ${processedItems.length} items.`
      }]
    };
  } catch (e) {
    if (e.name === "AbortError") {
      // Rollback any partially completed work
      for (const rollback of rollbackActions.reverse()) {
        await rollback();
      }
      return {
        content: [{
          type: "text",
          text: `Cancelled. Rolled back ${processedItems.length} items.`
        }]
      };
    }
    throw e;
  }
}
```

## Relationship to [Issue #9 — outputSchema](https://github.com/webmachinelearning/webmcp/issues/9)

From [Issue #48](https://github.com/webmachinelearning/webmcp/issues/48):

> If we decide to go with [Issue #9 — outputSchema](https://github.com/webmachinelearning/webmcp/issues/9) then maybe we need defined error
> values. So the author can either return a value based on the output schema (success)
> or one of the error codes (failure/abort).

Potential error classification:

| Outcome | Mechanism | Agent Interpretation |
|---|---|---|
| Success | Return value | Tool completed, use the result |
| Tool error | `throw new Error(...)` | Tool failed, try alternative approach |
| User cancel | `throw new Error("cancelled")` | User rejected, don't retry |
| Abort | `throw new DOMException("Aborted", "AbortError")` | External cancellation |

## AbortSignal.any() Pattern

For tools that combine multiple abortable operations:

```javascript
execute: async ({ urls }, client) => {
  // Create a per-operation controller that also respects the agent's signal
  const results = await Promise.all(
    urls.map(async (url) => {
      const response = await fetch(url, { signal: client.signal });
      return response.json();
    })
  );

  return {
    content: [{
      type: "text",
      text: JSON.stringify(results)
    }]
  };
}
```

## Best Practices

1. **Always check for abort in long-running operations** — Don't block indefinitely
2. **Pass the signal to fetch()** — Native integration, no extra code needed
3. **Clean up resources on abort** — Close connections, terminate workers, rollback state
4. **Use `AbortError` name** — Standard web platform convention for cancellation errors
5. **Don't ignore abort** — Even if you can't fully cancel, acknowledge and return quickly

## Related Issues

- [Issue #48 — Add a callback for abort](https://github.com/webmachinelearning/webmcp/issues/48) — Primary discussion
- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Error codes in output
- [Issue #21 — Elicitation](https://github.com/webmachinelearning/webmcp/issues/21) — User interaction during cancellation

## See Also

- [Execute callback specification](execute-callback.md)
- [registerTool() method](register-tool.md)
- [Tool registration interface](tool-registration-interface.md)
- [WebIDL definitions](webidl-definitions.md)
- [WebMCP events](events.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [User takeover during execution](../06-user-interaction/user-takeover.md)
- [Concurrent execution](../07-architecture/concurrent-execution.md)
- [Background tool execution](../05-service-workers/background-tool-execution.md)
- [Error handling](../15-reference/error-handling.md)
