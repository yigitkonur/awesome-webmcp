# User Takeover During Tool Execution

> Issue #20: What happens when a user "takes over" during agent tool execution — resolution, current state, and how elicitation handles the handoff instead.

## Status

- **Issue**: [#20](https://github.com/webmachinelearning/webmcp/issues/20) — Interleaving user and agent interaction
- **Resolution (Oct 16, 2025)**: No concrete use case identified yet. Elicitation (Issue #21) handles the site-initiated handoff case.
- **Related**: [#21](https://github.com/webmachinelearning/webmcp/issues/21) (requestUserInteraction), [#48](https://github.com/webmachinelearning/webmcp/issues/48) (AbortSignal)

---

## The Question

What should happen when a user decides to take over during agent tool execution? Consider scenarios where:

1. An agent is executing a multi-step tool
2. The user wants to intervene mid-execution
3. The user starts interacting with the page directly while the tool is running

```
┌──────────────────────────────────────────────────────┐
│              USER TAKEOVER SCENARIO                    │
│                                                       │
│  Timeline:                                            │
│  ─────────────────────────────────────────────        │
│  │  Agent starts tool  │  User clicks something │     │
│  │  execution          │  on the page            │     │
│  ─────────────────────────────────────────────        │
│                        ▲                              │
│                        │                              │
│              WHAT HAPPENS HERE?                       │
│              • Should tool execution stop?            │
│              • Should the site be informed?           │
│              • Does the agent step back?              │
│              • How does state get reconciled?         │
└──────────────────────────────────────────────────────┘
```

---

## Resolution: No Concrete Use Case Yet

The October 16, 2025 resolution determined:

> **"No concrete use case identified. Elicitation (Issue #21) handles the site-initiated handoff case."**

This means:
1. **User-initiated takeover** (user spontaneously starts interacting) has no defined mechanism
2. **Site-initiated handoff** (tool asks user for input) is handled by `requestUserInteraction()`
3. The distinction is important: WebMCP currently supports only the site/tool asking the user, not the user interrupting the agent

---

## How Elicitation Handles It Instead

The `requestUserInteraction()` API (Issue #21) addresses the most important use case — when the tool **needs** user input:

```js
navigator.modelContext.registerTool({
  name: "configure-product",
  description: "Configure a product with user-selected options",
  execute: async (params, agent) => {
    // Agent handles initial setup
    await loadProductConfig(params.productId);
    
    // Tool explicitly requests user input (site-initiated handoff)
    const userChoices = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        showConfigUI();
        document.getElementById("config-done").onclick = () => {
          resolve(getSelectedOptions());
        };
      });
    });
    
    // Agent continues with user's choices
    const result = await applyConfiguration(userChoices);
    return { status: "configured", options: userChoices };
  }
});
```

### Key Difference

| Scenario | Mechanism | Status |
|----------|-----------|--------|
| **Site asks user for input** | `requestUserInteraction()` | ✅ Resolved (Issue #21) |
| **User spontaneously takes over** | Not defined | ❌ No use case identified |
| **Agent cancels execution** | `AbortSignal` | ✅ Resolved (Issue #48) |

---

## Unresolved Scenarios

### Scenario 1: User Clicks While Tool Runs

User starts clicking around the page while an agent tool is executing:

```
Agent: Executing "fill-shipping-form" tool...
User: *clicks on a product link*
      *navigates to a different page*
```

**Questions**:
- Does the tool execution abort?
- Does the agent receive an error?
- Is the navigation blocked?
- Who has "priority" — the user or the agent?

### Scenario 2: User Modifies State

User changes the page state that the tool depends on:

```
Agent: Executing "process-cart" tool...
       Step 1: Read cart contents ✅
       Step 2: Apply discount code...
User:  *removes an item from cart*
       Step 3: Calculate total... (cart state changed!)
```

### Scenario 3: Conflicting Actions

User and agent try to do conflicting things simultaneously:

```
Agent: Submitting form with value "Option A"
User:  Selecting "Option B" in the same form
```

---

## Future Considerations

### Potential Resolution Approaches

**Approach 1: Agent Yields on User Activity**
```js
// Hypothetical future API
navigator.modelContext.registerTool({
  name: "long-running-task",
  execute: async (params, agent) => {
    // Listen for user takeover
    agent.onUserTakeover = () => {
      // Gracefully pause or abort
      return { status: "paused", resumable: true };
    };
    
    // ... long-running execution
  }
});
```

**Approach 2: Mutual Exclusion**
```js
// Hypothetical: browser prevents user interaction during tool execution
// (likely too restrictive)
```

**Approach 3: State Reconciliation**
```js
// Hypothetical: tool receives notification of state changes
navigator.modelContext.registerTool({
  name: "multi-step-task",
  execute: async (params, agent) => {
    agent.onStateChange = (change) => {
      // Reconcile state changes made by user during execution
      console.log("User changed:", change);
    };
    
    // ... execution continues with awareness of changes
  }
});
```

### Why No Use Case Was Found

The resolution noted that:
1. Most tools complete quickly — user takeover during a brief tool call is unlikely
2. `requestUserInteraction()` covers the "tool needs user" case adequately
3. `AbortSignal` covers the "cancel tool execution" case
4. The remaining scenarios (user spontaneously intervening) are rare and complex

### When This Might Be Revisited

- Long-running tool executions become common
- Agent workflows span multiple tools with observable UI changes
- Multi-agent scenarios where multiple agents and users interact simultaneously
- Complex form-filling workflows where both agent and user contribute

---

## Related: AbortSignal for Cancellation

While not user takeover, the `AbortSignal` pattern (Issue #48) provides a way to cancel tool execution:

```js
execute(input, { signal }) {
  signal.addEventListener('abort', () => {
    // Roll back operation, reset UI
    rollbackChanges();
    resetUI();
  });
  
  // Long-running operation that checks for abort
  for (const step of steps) {
    if (signal.aborted) {
      return { status: "aborted" };
    }
    await processStep(step);
  }
}
```

**Resolution (Jan 22, 2026)**: Use `AbortSignal` to signal cancellation. The browser creates an `AbortController` per tool call and passes the signal.

---

## See Also

- [requestUserInteraction() API](request-user-interaction.md)
- [Human-in-the-loop philosophy](human-in-the-loop.md)
- [agentInvoked flag](agent-invoked-flag.md)
- [Elicitation design patterns](elicitation-patterns.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [User gesture activation](../04-security/user-gesture-activation.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [AbortSignal for cancellation](../02-specification/abort-signal.md)
- [WebMCP events](../02-specification/events.md)
- [Concurrent execution](../07-architecture/concurrent-execution.md)

## References

- [Issue #20](https://github.com/webmachinelearning/webmcp/issues/20) — Interleaving user and agent interaction
- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #48](https://github.com/webmachinelearning/webmcp/issues/48) — Tool abort via AbortSignal
- [Issue #50](https://github.com/webmachinelearning/webmcp/issues/50) — Multiple elicitation requests
