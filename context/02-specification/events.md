# WebMCP Events

## Overview

WebMCP defines an event-based interaction model alongside the callback-based API
(see [spec](https://webmachinelearning.github.io/webmcp)).
Events allow web pages to react to agent tool invocations, handle cancellations
([Issue #20 — user takeover](https://github.com/webmachinelearning/webmcp/issues/20)),
and integrate with existing form-based workflows. The event system is particularly important
for the hybrid approach described in the [API proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md), where declarative (HTML) and
imperative (JS) tool definitions coexist.

## Event Types

### toolcall Event

The `toolcall` event fires on every incoming tool call **before** executing the tool's
`execute` function. This enables a hybrid approach where event handlers can intercept
tool calls.

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> A `"toolcall"` event is dispatched on every incoming tool call _before_ executing
> the tool's `execute` function. The event handler can handle the tool call by calling
> the event's `preventDefault()` method, and then responding to the agent with
> `respondWith()` as shown above. If the event handler does not call `preventDefault()`
> then the browser's default behavior for tool calls will occur.

#### Usage Pattern

```javascript
// Event-based tool call handling
window.addEventListener("toolcall", async (e) => {
  if (e.name === "add-todo") {
    e.preventDefault(); // Prevent default execute callback

    const { text } = e.input;
    addTodoItem(text);

    e.respondWith({
      content: [
        { type: "text", text: `Todo "${text}" added via event handler.` }
      ]
    });
    return;
  }

  // Other tool calls fall through to their execute callbacks
});
```

#### respondWith() Method

Inspired by the Service Worker `fetch` event, `respondWith()` allows the event handler
to provide a response to the agent:

```javascript
e.respondWith(/* structured content response */);
```

This is analogous to `FetchEvent.respondWith()` in Service Workers — the event handler
takes responsibility for providing the response instead of letting the default behavior occur.

#### Hybrid Approach Flow

```
1. Agent sends tool call for "add-todo"
2. Browser dispatches "toolcall" event
3. If event handler calls preventDefault():
   → Event handler responds via respondWith()
   → execute callback is NOT called
4. If event handler does NOT call preventDefault():
   → execute callback for "add-todo" is called
   → If no tool named "add-todo" exists, agent receives error
```

### submit Event with agentInvoked Property

When tools interact with HTML forms (declarative API), the standard `submit` event fires with additional
context indicating whether it was triggered by an agent:

```javascript
document.getElementById("addStampForm").addEventListener("submit", (event) => {
  event.preventDefault();

  if (event.agentInvoked) {
    // Submitted by an AI agent — may want different UX
    console.log("Form submitted by agent");

    // Return structured data to the agent via respondWith()
    event.respondWith(Promise.resolve({
      content: [{ type: "text", text: "Stamp added successfully" }]
    }));
  } else {
    // Submitted by human user
    console.log("Form submitted by user");
  }

  const stampName = document.getElementById("stampName").value;
  addStamp(stampName);
});
```

This enables web developers to differentiate between human and agent interactions,
allowing adaptive UI behavior.

#### SubmitEvent.respondWith()

When a form is submitted by an agent (`event.agentInvoked === true`), the `respondWith()` method
allows the form handler to return a structured response back to the agent as tool output. This
follows the Service Worker `FetchEvent.respondWith()` pattern. After calling `preventDefault()`,
the handler can provide the agent with structured errors or results, enabling self-correction.

### toolactivated Event

The `toolactivated` event fires when a tool invocation starts. This allows the page
to update its UI to reflect that an agent operation is in progress:

```javascript
navigator.modelContext.addEventListener("toolactivated", (e) => {
  // Show loading indicator
  document.body.classList.add("agent-active");
  showNotification(`Agent is using tool: ${e.toolName}`);
});
```

#### Use Cases

- Display a loading spinner or progress indicator
- Disable form inputs to prevent conflicts
- Show a banner indicating "Agent is working..."
- Log tool invocations for debugging

### toolcancel Event

The `toolcancel` event fires when a tool invocation is cancelled (by the user or agent),
related to [Issue #21 — elicitation](https://github.com/webmachinelearning/webmcp/issues/21):

```javascript
navigator.modelContext.addEventListener("toolcancel", (e) => {
  // Clean up UI state
  document.body.classList.remove("agent-active");
  hideNotification();
  console.log(`Tool ${e.toolName} was cancelled`);
});
```

## Event-Based Architecture (Manifest-Driven)

The proposal describes a future architecture where tools are defined declaratively
in a manifest and handled via events:

### Step 1: Define Tools in manifest.json

```json
{
  "tools": [
    {
      "name": "add-todo",
      "description": "Add a new todo item to the list",
      "inputSchema": {
        "type": "object",
        "properties": {
          "text": { "type": "string", "description": "The text of the todo item" }
        },
        "required": ["text"]
      }
    }
  ]
}
```

### Step 2: Handle Tool Calls as Events

```javascript
window.addEventListener("toolcall", async (e) => {
  if (e.name === "add-todo") {
    addTodoItem(e.input.text);
    e.respondWith({
      content: [{ type: "text", text: `Added: ${e.input.text}` }]
    });
    return;
  }
  // ... handle other tools
});
```

### Advantages

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> **Advantages:** Allows additional context different discovery mechanisms without
> rendering a page.

### Disadvantages

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> **Disadvantages:**
> - Slightly harder to keep definition and implementation in sync.
> - Potentially large switch-case in event handler.

## The Hybrid Approach

The recommended approach combines both callback-based and event-based patterns:

```javascript
// 1. Register tools with execute callbacks (primary approach)
navigator.modelContext.provideContext({
  tools: [
    {
      name: "add-todo",
      description: "Add a todo item",
      inputSchema: { /* ... */ },
      execute: ({ text }) => {
        addTodoItem(text);
        return { content: [{ type: "text", text: `Added: ${text}` }] };
      }
    }
  ]
});

// 2. Optionally intercept specific tool calls via events
window.addEventListener("toolcall", (e) => {
  // Log all tool calls
  analytics.track("tool_invoked", { name: e.name });

  // Intercept specific tools for custom handling
  if (e.name === "add-todo" && maintenanceMode) {
    e.preventDefault();
    e.respondWith({
      content: [{ type: "text", text: "System is in maintenance mode." }]
    });
  }
  // Otherwise, let the execute callback handle it
});
```

## Event Ordering

```
Agent calls tool "add-todo"
  ↓
1. "toolactivated" event fires
  ↓
2. "toolcall" event fires
  ↓
3a. If preventDefault() called:
    → Event handler responds with respondWith()
    → "toolcomplete" fires (future)
  ↓
3b. If NOT prevented:
    → execute callback runs
    → On success: result sent to agent
    → On failure: error sent to agent
    → On cancellation: "toolcancel" event fires
```

## Adaptive UI with Events

From the [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md):

> The author may also wish to change the on-page user experience when a client is
> connected. For example, if the user is interacting with the page primarily through
> an AI agent or assistive tool, then the author might choose to disable or hide the
> HTML form input and use more of the available space to show the stamp collection.

```javascript
// Track agent activity for adaptive UI
let activeToolCount = 0;

navigator.modelContext.addEventListener("toolactivated", () => {
  activeToolCount++;
  if (activeToolCount === 1) {
    enterAgentMode();
  }
});

navigator.modelContext.addEventListener("toolcancel", () => {
  activeToolCount = Math.max(0, activeToolCount - 1);
  if (activeToolCount === 0) {
    exitAgentMode();
  }
});

function enterAgentMode() {
  document.querySelector(".human-form").style.display = "none";
  document.querySelector(".agent-status").style.display = "block";
}

function exitAgentMode() {
  document.querySelector(".human-form").style.display = "block";
  document.querySelector(".agent-status").style.display = "none";
}
```

## Pattern: Form-Tool Integration

Integrate existing HTML forms with WebMCP tools:

```javascript
// Existing form handler
const form = document.getElementById("addStampForm");

form.addEventListener("submit", (event) => {
  event.preventDefault();
  const data = new FormData(form);
  addStamp(data.get("name"), data.get("description"), data.get("year"));
});

// WebMCP tool that reuses the same logic
navigator.modelContext.registerTool({
  name: "add-stamp",
  description: "Add a new stamp to the collection",
  inputSchema: {
    type: "object",
    properties: {
      name: { type: "string" },
      description: { type: "string" },
      year: { type: "number" }
    },
    required: ["name", "description", "year"]
  },
  execute: ({ name, description, year }) => {
    addStamp(name, description, year);
    return {
      content: [{ type: "text", text: `Stamp "${name}" added.` }]
    };
  }
});
```

## Related Issues

- [Issue #20 — User takeover](https://github.com/webmachinelearning/webmcp/issues/20) — Agent activity and user takeover
- [Issue #21 — Elicitation](https://github.com/webmachinelearning/webmcp/issues/21) — User interaction during tool execution
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — HTML-based tool definitions and events
- [Issue #48 — Abort callback](https://github.com/webmachinelearning/webmcp/issues/48) — Cancellation events

## See Also

- [navigator.modelContext API](navigator-model-context.md)
- [registerTool() method](register-tool.md)
- [unregisterTool() and clearContext()](unregister-tool.md)
- [CSS pseudo-classes for agent state](css-pseudo-classes.md)
- [AbortSignal for cancellation](abort-signal.md)
- [agentInvoked flag](../06-user-interaction/agent-invoked-flag.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Tool auto-discovery](../03-declarative-api/auto-discovery.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)
