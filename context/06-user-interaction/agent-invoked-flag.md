# The `agentInvoked` Property on Events

> Differentiating agent-initiated vs human-initiated actions in WebMCP — the `agentInvoked` flag on submit events, conditional handling patterns, and the `preventDefault` + `respondWith` pattern.

## Status

- **Issue**: [#62](https://github.com/webmachinelearning/webmcp/issues/62) — User gesture activation / agentInvoked
- **Related**: [#21](https://github.com/webmachinelearning/webmcp/issues/21) (requestUserInteraction), [#44](https://github.com/webmachinelearning/webmcp/issues/44) (Permissions)
- **Concept Origin**: Discussions around differentiating agent and human form submissions

---

## Overview

The `agentInvoked` property is a boolean flag on DOM events (primarily `submit` events) that indicates whether the event was triggered by an AI agent via WebMCP or by a human user through direct interaction.

This enables websites to apply **different handling logic** depending on the source of the action — adding safeguards for agent-initiated actions while keeping the human experience frictionless.

```
┌──────────────────────────────────────────────────┐
│            EVENT SOURCE DETECTION                 │
│                                                   │
│  Human clicks "Submit"  ──▶  event.agentInvoked = false
│  Agent calls tool       ──▶  event.agentInvoked = true
│                                                   │
│  ┌───────────────────────────────────────────┐    │
│  │  if (event.agentInvoked) {                │    │
│  │    // Extra safeguards for agent actions   │    │
│  │    showConfirmation();                     │    │
│  │  } else {                                 │    │
│  │    // Normal human flow                   │    │
│  │    processSubmission();                    │    │
│  │  }                                        │    │
│  └───────────────────────────────────────────┘    │
└──────────────────────────────────────────────────┘
```

---

## Why This Matters

### Different Trust Levels

When a human clicks a "Buy Now" button, they have:
- Seen the product page
- Reviewed the price
- Made a conscious decision to click

When an agent submits the same form, the user may not have:
- Seen the exact product
- Reviewed the final price
- Consciously decided to purchase

The `agentInvoked` flag enables sites to add appropriate safeguards.

### Progressive Enhancement

Sites can progressively enhance their agent support without breaking the human experience:

```js
// This event listener works for both humans and agents
form.addEventListener("submit", (event) => {
  if (event.agentInvoked) {
    // Agent path: add confirmation
    handleAgentSubmission(event);
  } else {
    // Human path: proceed normally
    handleHumanSubmission(event);
  }
});
```

---

## The `preventDefault` + `respondWith` Pattern

When an agent triggers a form submission, the site can intercept it using `preventDefault()` and then use `respondWith()` to return a structured response to the agent:

```js
document.querySelector("#checkout-form").addEventListener("submit", (event) => {
  if (event.agentInvoked) {
    event.preventDefault();
    
    event.respondWith(async () => {
      // Show confirmation dialog to the user
      const confirmed = await showCheckoutConfirmation({
        items: getCartItems(),
        total: getCartTotal()
      });
      
      if (confirmed) {
        const order = await processCheckout();
        return {
          status: "completed",
          orderId: order.id,
          total: order.total
        };
      }
      
      return {
        status: "cancelled",
        reason: "User declined checkout"
      };
    });
  }
  // If not agentInvoked, normal form submission proceeds
});
```

### Pattern Breakdown

1. **`event.agentInvoked`**: Check if the submission was triggered by an agent
2. **`event.preventDefault()`**: Stop the default form submission behavior
3. **`event.respondWith(async () => { ... })`**: Provide an async callback that:
   - Can show UI to the user (via `requestUserInteraction`)
   - Can perform validation or additional checks
   - Returns a structured response to the agent
   - The agent receives the return value as the tool result

---

## Conditional Handling Examples

### Example 1: Payment Form

```js
const paymentForm = document.querySelector("#payment-form");

paymentForm.addEventListener("submit", (event) => {
  if (event.agentInvoked) {
    event.preventDefault();
    
    event.respondWith(async () => {
      // Agent-triggered: require explicit user confirmation
      const paymentDetails = extractFormData(event.target);
      
      const userApproved = await showPaymentConfirmation({
        amount: paymentDetails.amount,
        recipient: paymentDetails.recipient,
        method: paymentDetails.paymentMethod
      });
      
      if (!userApproved) {
        return { status: "declined", message: "User declined payment" };
      }
      
      const result = await processPayment(paymentDetails);
      return { 
        status: "completed",
        transactionId: result.id,
        amount: result.amount
      };
    });
  } else {
    // Human-triggered: user already reviewed the form
    processPayment(extractFormData(event.target));
  }
});
```

### Example 2: Content Deletion

```js
document.querySelector("#delete-btn").addEventListener("click", (event) => {
  if (event.agentInvoked) {
    event.preventDefault();
    
    event.respondWith(async () => {
      // Double-confirm for agent-initiated deletions
      const confirmed = await showDeletionWarning({
        itemName: getSelectedItem().name,
        permanent: true,
        triggeredBy: "AI Agent"
      });
      
      if (!confirmed) {
        return { status: "cancelled" };
      }
      
      await deleteItem(getSelectedItem().id);
      return { status: "deleted", itemId: getSelectedItem().id };
    });
  } else {
    // Human already clicked delete intentionally
    if (confirm("Are you sure you want to delete this item?")) {
      deleteItem(getSelectedItem().id);
    }
  }
});
```

### Example 3: Search (No Extra Safeguards Needed)

```js
document.querySelector("#search-form").addEventListener("submit", (event) => {
  // Search is safe for both agents and humans — no conditional needed
  const query = event.target.querySelector("input[name='q']").value;
  performSearch(query);
  
  if (event.agentInvoked) {
    event.preventDefault();
    event.respondWith(async () => {
      const results = await performSearch(query);
      return { results: results.slice(0, 10) };
    });
  }
});
```

---

## When to Check `agentInvoked`

### Always Check

| Action Type | Why |
|------------|-----|
| **Purchases/payments** | Financial consequence, requires conscious user decision |
| **Account changes** | Password, email, permissions — high impact |
| **Data deletion** | Irreversible actions need confirmation |
| **External sharing** | Data leaving the site boundary |
| **Communication** | Sending emails, messages on behalf of user |

### Optional Check

| Action Type | Why |
|------------|-----|
| **Search/browse** | Low risk, read-only |
| **Preferences** | Easily reversible |
| **Display changes** | No data impact |
| **Cart management** | Additions are reversible |

### Never Needed

| Action Type | Why |
|------------|-----|
| **Read-only data access** | No side effects |
| **Analytics/tracking** | Site-internal |
| **UI state** | Visual only |

---

## Relationship to `toolautosubmit`

The `toolautosubmit` HTML attribute and `agentInvoked` work together:

```html
<!-- toolautosubmit: agent can auto-submit this form -->
<form toolautosubmit id="search-form">
  <input name="query" type="text" />
  <button type="submit">Search</button>
</form>

<!-- No toolautosubmit: agent cannot auto-submit -->
<form id="payment-form">
  <input name="amount" type="number" />
  <button type="submit">Pay</button>
</form>
```

```js
// Even with toolautosubmit, agentInvoked is still set
document.querySelector("#search-form").addEventListener("submit", (event) => {
  if (event.agentInvoked) {
    // Agent auto-submitted this form via toolautosubmit
    console.log("Agent performed search");
  }
});
```

---

## Implementation Notes

### Browser Responsibility

The browser sets `agentInvoked = true` when:
- An agent calls a WebMCP tool that triggers a form submission
- An agent interacts with the page through tool execution
- The event chain originated from an agent tool call

### Cannot Be Spoofed

`agentInvoked` is set by the browser, not by JavaScript. Site scripts cannot:
- Set `agentInvoked = true` on manually created events
- Override the browser's determination
- Pretend an agent action was human or vice versa

---

## See Also

- [requestUserInteraction() API](request-user-interaction.md)
- [Human-in-the-loop philosophy](human-in-the-loop.md)
- [User takeover during execution](user-takeover.md)
- [Elicitation design patterns](elicitation-patterns.md)
- [User gesture activation](../04-security/user-gesture-activation.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [Security threat model](../04-security/threat-model-overview.md)
- [WebMCP events](../02-specification/events.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [CSS pseudo-classes for agent state](../02-specification/css-pseudo-classes.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)

## References

- [Issue #62](https://github.com/webmachinelearning/webmcp/issues/62) — User gesture activation / agentInvoked
- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) — Action-specific permissions
