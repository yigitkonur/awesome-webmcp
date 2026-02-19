# Elicitation Design Patterns

> Practical design patterns for using `requestUserInteraction()` in WebMCP — confirmation dialogs, payment flows, sensitive data entry, multi-step wizards, and handling abusive sites.

## Status

- **Primary Issue**: [#21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- **Related**: [#50](https://github.com/webmachinelearning/webmcp/issues/50) (Multiple elicitation), [#44](https://github.com/webmachinelearning/webmcp/issues/44) (Permissions), [#20](https://github.com/webmachinelearning/webmcp/issues/20) (User takeover)
- **Key Contributors**: Brandon Walderman (Microsoft), Khushal Sagar (Google), Ilya Grigorik (Shopify), stalgiag

---

## Overview

Elicitation is the process of a WebMCP tool requesting user input during execution. The `requestUserInteraction()` API enables rich, interactive user experiences within agent workflows — from simple confirmations to complex multi-step wizards.

```
┌───────────────────────────────────────────────────────┐
│                ELICITATION PATTERNS                    │
│                                                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │Confirm   │  │Payment   │  │Sensitive │            │
│  │Dialog    │  │Flow      │  │Data Entry│            │
│  └──────────┘  └──────────┘  └──────────┘            │
│                                                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │Multi-Step│  │Terms &   │  │Identity  │            │
│  │Wizard    │  │Conditions│  │Verify    │            │
│  └──────────┘  └──────────┘  └──────────┘            │
└───────────────────────────────────────────────────────┘
```

---

## Pattern 1: Confirmation Dialog

The simplest and most common elicitation pattern — asking the user to confirm an action before proceeding.

### Basic Confirmation

```js
navigator.modelContext.registerTool({
  name: "delete-all-emails",
  description: "Delete all emails in the selected folder",
  annotations: { destructiveHint: true },
  execute: async (params, agent) => {
    const emailCount = await getEmailCount(params.folder);
    
    const confirmed = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const dialog = document.createElement("dialog");
        dialog.innerHTML = `
          <h2>⚠️ Delete ${emailCount} emails?</h2>
          <p>This will permanently delete all emails in "${params.folder}".</p>
          <p>This action cannot be undone.</p>
          <div class="actions">
            <button id="cancel">Cancel</button>
            <button id="confirm" class="danger">Delete All</button>
          </div>
        `;
        document.body.appendChild(dialog);
        dialog.showModal();
        
        dialog.querySelector("#confirm").onclick = () => {
          dialog.close();
          dialog.remove();
          resolve(true);
        };
        dialog.querySelector("#cancel").onclick = () => {
          dialog.close();
          dialog.remove();
          resolve(false);
        };
      });
    });
    
    if (!confirmed) {
      return { status: "cancelled", message: "Deletion cancelled by user" };
    }
    
    await deleteEmails(params.folder);
    return { status: "deleted", count: emailCount };
  }
});
```

### When to Use

- Irreversible actions (deletions, purchases, transfers)
- Actions affecting other users (sending messages, publishing content)
- High-value operations (financial transactions, account changes)

---

## Pattern 2: Payment Flow

Payment flows require careful elicitation with multiple user interaction points for compliance and security.

### E-Commerce Checkout

```js
navigator.modelContext.registerTool({
  name: "checkout",
  description: "Complete purchase of items in the shopping cart",
  annotations: { destructiveHint: true, readOnlyHint: false },
  execute: async (params, agent) => {
    // Step 1: Show order summary and get confirmation
    const orderReview = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const cart = getCartContents();
        const reviewUI = document.getElementById("order-review");
        
        reviewUI.querySelector(".items").innerHTML = cart.items
          .map(i => `<li>${i.name} - $${i.price}</li>`)
          .join("");
        reviewUI.querySelector(".total").textContent = `$${cart.total}`;
        reviewUI.querySelector(".shipping").textContent = params.shippingMethod;
        reviewUI.style.display = "block";
        
        reviewUI.querySelector("#proceed").onclick = () => {
          resolve({ confirmed: true, total: cart.total });
        };
        reviewUI.querySelector("#cancel").onclick = () => {
          resolve({ confirmed: false });
        };
      });
    });
    
    if (!orderReview.confirmed) {
      return { status: "cancelled" };
    }
    
    // Step 2: Payment authorization (separate interaction for PCI compliance)
    const paymentAuth = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const paymentUI = document.getElementById("payment-auth");
        paymentUI.querySelector(".amount").textContent = `$${orderReview.total}`;
        paymentUI.style.display = "block";
        
        // User selects payment method and enters CVV
        paymentUI.querySelector("#authorize").onclick = () => {
          const method = paymentUI.querySelector("#payment-method").value;
          const cvv = paymentUI.querySelector("#cvv").value;
          resolve({ authorized: true, method, cvv });
        };
        paymentUI.querySelector("#cancel").onclick = () => {
          resolve({ authorized: false });
        };
      });
    });
    
    if (!paymentAuth.authorized) {
      return { status: "payment_cancelled" };
    }
    
    // Step 3: Process payment (autonomous)
    const order = await processOrder(paymentAuth);
    return { 
      status: "completed",
      orderId: order.id,
      total: order.total,
      estimatedDelivery: order.delivery
    };
  }
});
```

### Ilya Grigorik's Commerce Requirements

> "The agent can build a cart and prefill the information, but then the tool needs a mechanism to indicate that hand-off is required."

Commerce-specific requirements that necessitate elicitation:
- **Terms and conditions**: Legal requirement for user acceptance
- **Disclosures**: Regulatory requirement for user review
- **Liability**: User must explicitly authorize financial transactions
- **Compliance**: PCI DSS requires user involvement in payment flows

---

## Pattern 3: Sensitive Data Entry

When tools need sensitive data that the agent should never see or handle.

### Password Entry

```js
navigator.modelContext.registerTool({
  name: "change-password",
  description: "Change the user's account password",
  annotations: { destructiveHint: true },
  execute: async (params, agent) => {
    // Agent NEVER handles passwords — user enters directly
    const passwords = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const form = document.getElementById("password-form");
        form.style.display = "block";
        
        form.addEventListener("submit", (e) => {
          e.preventDefault();
          const current = form.querySelector("#current-password").value;
          const newPwd = form.querySelector("#new-password").value;
          const confirm = form.querySelector("#confirm-password").value;
          
          if (newPwd !== confirm) {
            form.querySelector(".error").textContent = "Passwords don't match";
            return;
          }
          
          form.style.display = "none";
          resolve({ current, newPassword: newPwd });
        });
      });
    });
    
    // Process on server — passwords never returned to agent
    const result = await updatePassword(passwords.current, passwords.newPassword);
    
    // Return status only, no sensitive data
    return { 
      status: result.success ? "changed" : "failed",
      message: result.success ? "Password updated" : result.error
    };
  }
});
```

### Two-Factor Authentication

```js
navigator.modelContext.registerTool({
  name: "verify-identity",
  description: "Verify user identity with 2FA code",
  execute: async (params, agent) => {
    // Send 2FA code
    await send2FACode(params.method); // SMS, email, or authenticator
    
    // User enters 2FA code directly
    const code = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const codeInput = document.getElementById("2fa-input");
        codeInput.style.display = "block";
        codeInput.querySelector("input").focus();
        
        codeInput.querySelector("form").onsubmit = (e) => {
          e.preventDefault();
          resolve(codeInput.querySelector("input").value);
        };
      });
    });
    
    const verified = await verify2FACode(code);
    return { 
      status: verified ? "verified" : "failed",
      // Never return the code back to the agent
    };
  }
});
```

---

## Pattern 4: Multi-Step Wizard

Complex workflows that require multiple interactions across several steps.

### Travel Booking Wizard

```js
navigator.modelContext.registerTool({
  name: "book-vacation",
  description: "Book a complete vacation package with flights, hotel, and activities",
  execute: async (params, agent) => {
    // Step 1: Agent searches options (autonomous)
    const flights = await searchFlights(params.destination, params.dates);
    const hotels = await searchHotels(params.destination, params.dates);
    const activities = await findActivities(params.destination);
    
    // Step 2: User selects flight
    const selectedFlight = await agent.requestUserInteraction(async () => {
      return await showFlightSelector(flights);
    });
    
    if (!selectedFlight) {
      return { status: "cancelled", step: "flight_selection" };
    }
    
    // Step 3: User selects hotel
    const selectedHotel = await agent.requestUserInteraction(async () => {
      return await showHotelSelector(hotels, {
        checkIn: params.dates.start,
        checkOut: params.dates.end
      });
    });
    
    if (!selectedHotel) {
      return { status: "cancelled", step: "hotel_selection" };
    }
    
    // Step 4: User picks activities
    const selectedActivities = await agent.requestUserInteraction(async () => {
      return await showActivityPicker(activities, params.dates);
    });
    
    // Step 5: Agent calculates total (autonomous)
    const total = calculatePackageTotal(selectedFlight, selectedHotel, selectedActivities);
    
    // Step 6: User confirms and pays
    const confirmation = await agent.requestUserInteraction(async () => {
      return await showBookingSummary({
        flight: selectedFlight,
        hotel: selectedHotel,
        activities: selectedActivities,
        total: total
      });
    });
    
    if (!confirmation.approved) {
      return { status: "cancelled", step: "final_confirmation" };
    }
    
    // Step 7: Process booking (autonomous)
    const booking = await processBooking({
      flight: selectedFlight,
      hotel: selectedHotel,
      activities: selectedActivities,
      payment: confirmation.paymentMethod
    });
    
    return {
      status: "booked",
      confirmationCode: booking.code,
      itinerary: booking.itinerary
    };
  }
});
```

### Wizard Pattern Summary

```
Agent (autonomous) ──▶ User (interactive) ──▶ Agent ──▶ User ──▶ Agent
   Search options        Select option        Calculate   Confirm    Process
```

The wizard pattern alternates between:
- **Agent steps**: Data gathering, searching, calculating, processing
- **User steps**: Selection, confirmation, data entry, review

---

## Pattern 5: Terms and Legal Acceptance

Required for compliance in many jurisdictions.

```js
navigator.modelContext.registerTool({
  name: "create-account",
  description: "Create a new user account",
  execute: async (params, agent) => {
    // Agent prefills form data (autonomous)
    const accountData = prepareAccountData(params);
    
    // User must read and accept terms (legal requirement)
    const termsAccepted = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const termsUI = document.getElementById("terms-acceptance");
        termsUI.querySelector(".terms-text").src = "/legal/terms-of-service";
        termsUI.querySelector(".privacy-text").src = "/legal/privacy-policy";
        termsUI.style.display = "block";
        
        const checkbox = termsUI.querySelector("#accept-terms");
        const submitBtn = termsUI.querySelector("#create-btn");
        
        // Button only enabled after checkbox is checked
        checkbox.onchange = () => {
          submitBtn.disabled = !checkbox.checked;
        };
        
        submitBtn.onclick = () => {
          resolve({
            accepted: true,
            timestamp: new Date().toISOString()
          });
        };
        
        termsUI.querySelector("#decline-btn").onclick = () => {
          resolve({ accepted: false });
        };
      });
    });
    
    if (!termsAccepted.accepted) {
      return { status: "declined", reason: "User declined terms of service" };
    }
    
    const account = await createAccount(accountData, termsAccepted.timestamp);
    return { status: "created", accountId: account.id };
  }
});
```

---

## Handling Abusive Sites

### The Abuse Vector

Khushal Sagar identified the core concern:

> "The site intentionally keeps calling `requestUserInteraction` during legitimate tool calls. Say the tool is `addProduct` and the site says 'look at this offer first', requiring the user to hit a button to dismiss."

### Blocking Permanently (Resolution Oct 16, 2025)

When sites abuse `requestUserInteraction`, users can block them permanently:

```js
navigator.modelContext.registerTool({
  name: "add-product",
  execute: async (params, agent) => {
    try {
      // Abusive: unnecessary interaction request
      await agent.requestUserInteraction(async () => {
        // Shows promotional popup, not a real need for user input
        return await showPromoPopup("50% off if you buy now!");
      });
    } catch (error) {
      if (error.name === "NotAllowedError") {
        // ✅ User has permanently blocked this site's interactions
        // Implement fallback — proceed without interaction
        await addProductToCart(params.productId);
        return { status: "added", note: "Interactions blocked by user" };
      }
      throw error;
    }
    
    await addProductToCart(params.productId);
    return { status: "added" };
  }
});
```

### Browser-Level Protections

**stalgiag's observable lifecycle signals**:
- Browser emits yield start/end events tagged with execution ID
- Browser can rate-limit or downgrade attention during a single execution
- User affordance: "mute further prompts from this site/tool for this task"

### Rate Limiting Interactions

```
┌─────────────────────────────────────────────────┐
│         INTERACTION RATE LIMITING                │
│                                                  │
│  1st request:  ✅ Shown immediately              │
│  2nd request:  ✅ Shown normally                  │
│  3rd request:  ⚠️ Warning: "Site keeps asking"   │
│  4th request:  ⚠️ Collapsed, user can expand     │
│  5th request:  🚫 Auto-blocked, option to block  │
│                   site permanently               │
└─────────────────────────────────────────────────┘
```

---

## Multiple Concurrent Elicitations (Issue #50)

What happens when an agent triggers two tools that both need `requestUserInteraction`?

**Status**: No resolution yet. Depends on Issue #51 (in-page agent API).

### Potential Approaches

1. **Sequential**: Queue interactions, show one at a time
2. **Parallel**: Show both interactions simultaneously (complex UI)
3. **Priority-based**: Higher-priority tool gets interaction first

---

## Anti-Patterns

| ❌ Anti-Pattern | ✅ Better Approach |
|----------------|-------------------|
| Requesting interaction for read-only operations | Just return the data |
| Showing ads/promos via requestUserInteraction | Use regular ad placement |
| Requiring interaction for every tool call | Only for consequential actions |
| Not handling `NotAllowedError` | Always implement fallback |
| Requesting interaction for data the agent already has | Let the agent provide it |
| Multiple sequential confirmations for one action | Single comprehensive confirmation |

---

## See Also

- [requestUserInteraction() API](request-user-interaction.md)
- [Human-in-the-loop philosophy](human-in-the-loop.md)
- [agentInvoked flag](agent-invoked-flag.md)
- [User takeover during execution](user-takeover.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [Misrepresentation of intent](../04-security/misrepresentation.md)
- [Privacy leakage](../04-security/privacy-leakage.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [HTML form to JSON Schema mapping](../03-declarative-api/form-mapping.md)
- [Radio buttons and labels](../03-declarative-api/radio-buttons-and-labels.md)
- [Creative design use cases](../11-use-cases/creative-design.md)

## References

- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #50](https://github.com/webmachinelearning/webmcp/issues/50) — Multiple elicitation requests
- [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) — Action-specific permissions
- [Issue #20](https://github.com/webmachinelearning/webmcp/issues/20) — Interleaving user and agent interaction
