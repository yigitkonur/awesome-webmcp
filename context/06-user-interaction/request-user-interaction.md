# requestUserInteraction() API

> The mechanism for WebMCP tools to hand control back to the user mid-execution — enabling confirmations, forms, and rich user input during agent workflows.

## Status

- **Issue**: [#21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- **Filed By**: Brandon Walderman (Microsoft), September 2025
- **Resolution (Oct 2, 2025)**: Tool execution should be able to start/stop yielding to the user throughout its lifecycle
- **Resolution (Oct 16, 2025)**: `requestUserInteraction` should give users an option to block abusive sites permanently, but throw an error to developers so legitimate sites can implement fallback behavior
- **Related**: [#50](https://github.com/webmachinelearning/webmcp/issues/50) (Multiple elicitation), [#48](https://github.com/webmachinelearning/webmcp/issues/48) (AbortSignal)

---

## Overview

`requestUserInteraction()` is the primary mechanism for WebMCP tools to hand control back to the user during tool execution. It enables tools to pause autonomous agent operation and request explicit human input before continuing.

This is available on the `ModelContextClient` (the `agent` parameter passed to `execute()` callbacks).

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Agent   │───▶│ execute()│───▶│ request  │───▶│  User    │
│  calls   │    │ starts   │    │ User     │    │ provides │
│  tool    │    │          │    │ Inter-   │    │ input    │
│          │    │          │    │ action() │    │          │
└──────────┘    └──────────┘    └────┬─────┘    └────┬─────┘
                                     │               │
                                     │    callback    │
                                     │◀──resolves────▶│
                                     │               │
                                ┌────▼─────┐         │
                                │ execute()│         │
                                │ continues│         │
                                │ with     │         │
                                │ result   │         │
                                └──────────┘
```

---

## Core Pattern

### Basic Usage

```js
async function execute(input, agent) {
  const { a, b } = input;

  // User picks an operation manually
  const operation = await agent.requestUserInteraction(async () => {
    return await new Promise((resolve) => {
      const form = document.querySelector("#form");
      form.addEventListener('submit', e => {
        e.preventDefault();
        resolve(form.querySelector('select#operation').value);
      });
    });
  });

  // Continue with user's choice
  return `Result: ${a + b}`;
}
```

### How It Works

1. **Tool execution starts**: Agent calls `execute()` with parameters
2. **Tool requests user input**: Calls `agent.requestUserInteraction(callback)`
3. **Browser yields to user**: The page becomes interactive, user can interact with the UI
4. **Callback resolves**: When user completes interaction, callback returns a value
5. **Tool execution continues**: `execute()` continues with the user's response
6. **Tool returns result**: Final result sent back to agent

### Key Properties

- **Async**: Returns a `Promise` that resolves when the callback completes
- **Can be called multiple times**: A single tool execution can request user interaction at multiple points
- **Only available during tool execution**: Cannot be called outside of an active tool call
- **Blocking**: Tool execution pauses until user completes interaction

---

## The Confirm Pattern

The most common use case — requesting a yes/no confirmation before a destructive action:

```js
navigator.modelContext.registerTool({
  name: "purchase-items",
  description: "Complete the purchase of items in the cart",
  annotations: {
    destructiveHint: true,
    readOnlyHint: false
  },
  execute: async (params, agent) => {
    // Show cart summary and request confirmation
    const confirmed = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const dialog = document.getElementById("purchase-confirm");
        
        // Populate dialog with cart details
        dialog.querySelector(".total").textContent = `$${params.total}`;
        dialog.querySelector(".items").textContent = `${params.itemCount} items`;
        dialog.showModal();
        
        dialog.querySelector("#confirm-btn").onclick = () => {
          dialog.close();
          resolve(true);
        };
        dialog.querySelector("#cancel-btn").onclick = () => {
          dialog.close();
          resolve(false);
        };
      });
    });
    
    if (!confirmed) {
      return { status: "cancelled", message: "Purchase cancelled by user" };
    }
    
    const order = await processPurchase(params);
    return { status: "completed", orderId: order.id };
  }
});
```

---

## Multiple Interactions in One Tool Call

A tool can call `requestUserInteraction()` multiple times during a single execution:

```js
navigator.modelContext.registerTool({
  name: "book-flight",
  description: "Book a flight with seat selection and payment",
  execute: async (params, agent) => {
    // Step 1: User selects seat
    const seat = await agent.requestUserInteraction(async () => {
      return await showSeatPicker(params.flightId);
    });
    
    // Step 2: User confirms payment
    const paymentConfirmed = await agent.requestUserInteraction(async () => {
      return await showPaymentConfirmation({
        flight: params.flightId,
        seat: seat,
        price: params.price
      });
    });
    
    if (!paymentConfirmed) {
      return { status: "cancelled" };
    }
    
    // Step 3: User enters CVV (sensitive data entry)
    const cvv = await agent.requestUserInteraction(async () => {
      return await showCVVEntry();
    });
    
    const booking = await processBooking(params.flightId, seat, cvv);
    return { status: "booked", confirmation: booking.confirmationCode };
  }
});
```

---

## Abuse Prevention

### The Abuse Vector

Khushal Sagar identified the core abuse concern:

> "The site intentionally keeps calling `requestUserInteraction` during legitimate tool calls. Say the tool is `addProduct` and the site says 'look at this offer first', requiring the user to hit a button to dismiss."

### Safeguards

**Brandon Walderman's constraint**: `requestUserInteraction` is only available during an active tool call. No agent connected = no way to call this API.

**stalgiag's observable lifecycle signals**:
- Browser emits yield start/end events tagged with execution ID
- Browser can rate-limit or downgrade attention during a single execution
- User affordance: "mute further prompts from this site/tool for this task"

### Blocking Abusive Sites (Resolution Oct 16, 2025)

When a site abuses `requestUserInteraction`, the browser gives users the option to **block that site permanently**:

```js
// If user blocks the site, requestUserInteraction throws an error
navigator.modelContext.registerTool({
  name: "some-tool",
  execute: async (params, agent) => {
    try {
      const result = await agent.requestUserInteraction(async () => {
        return await showDialog();
      });
      return { result };
    } catch (error) {
      if (error.name === "NotAllowedError") {
        // User has blocked this site's interaction requests
        // Implement fallback behavior
        return { 
          status: "interaction_blocked",
          message: "Using default settings instead"
        };
      }
      throw error;
    }
  }
});
```

---

## Design Decisions

### Resolution: Start/Stop Yielding (Oct 2, 2025)

Tool execution should be able to start and stop yielding to the user throughout its lifecycle. This means:
- A tool can alternate between autonomous execution and user interaction
- The browser manages the transitions between agent control and user control
- Multiple yield points are supported in a single tool execution

### Resolution: Block Abusive Sites (Oct 16, 2025)

`requestUserInteraction` should give users an option to block abusive sites permanently, but throw an error to developers so legitimate sites can implement fallback behavior.

### Ilya Grigorik's Commerce Perspective

Shopify's Ilya Grigorik contributed a key insight on why this API is essential:

> "We need to have a path where a tool can signal to autonomous workflow that user input or review is required. With my commerce hat on, this is often due to terms, disclosures, liability & compliance requirements. The agent can build a cart and prefill the information, but then the tool needs a mechanism to indicate that hand-off is required."

---

## Related: AbortSignal (Issue #48)

Tool execution can be cancelled using the standard `AbortSignal` pattern:

```js
execute(input, { signal }) {
  signal.addEventListener('abort', () => {
    // Roll back operation, reset UI
  });
}
```

**Resolution (Jan 22, 2026)**: Use `AbortSignal` to signal cancellation. The browser creates an `AbortController` per tool call and passes the signal. Parallels the `NavigateEvent.signal` pattern in the Navigation API.

---

## See Also

- [Human-in-the-loop philosophy](human-in-the-loop.md)
- [agentInvoked flag](agent-invoked-flag.md)
- [User takeover during execution](user-takeover.md)
- [Elicitation design patterns](elicitation-patterns.md)
- [Misrepresentation of intent](../04-security/misrepresentation.md)
- [User gesture activation](../04-security/user-gesture-activation.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [Security threat model](../04-security/threat-model-overview.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [WebMCP events](../02-specification/events.md)
- [toolautosubmit attribute](../03-declarative-api/toolautosubmit-attribute.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)

## References

- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #48](https://github.com/webmachinelearning/webmcp/issues/48) — Tool abort via AbortSignal
- [Issue #50](https://github.com/webmachinelearning/webmcp/issues/50) — Multiple elicitation requests
- [Issue #20](https://github.com/webmachinelearning/webmcp/issues/20) — Interleaving user and agent interaction
