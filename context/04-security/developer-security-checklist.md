# Developer Security Checklist

> Complete security checklist for WebMCP tool developers — best practices for implementing safe, trustworthy, and secure tools that protect users from prompt injection, misrepresentation, and privacy attacks.

## Status

- **Derived From**: `_spec/docs/security-privacy-considerations.md`
- **Related Issues**: [#11](https://github.com/webmachinelearning/webmcp/issues/11), [#44](https://github.com/webmachinelearning/webmcp/issues/44), [#53](https://github.com/webmachinelearning/webmcp/issues/53), [#62](https://github.com/webmachinelearning/webmcp/issues/62)
- **Audience**: Web developers implementing WebMCP tools

---

## Quick Reference Checklist

```
✅ INPUT VALIDATION
  □ Validate all input parameters in execute()
  □ Sanitize strings — prevent injection in downstream systems
  □ Enforce type and range constraints beyond JSON Schema
  □ Reject unexpected or unknown parameters

✅ USER INTERACTION
  □ Use requestUserInteraction() for destructive/irreversible actions
  □ Check event.agentInvoked to differentiate agent vs human actions
  □ Show confirmation UI for purchases, deletions, account changes
  □ Set toolautosubmit only for idempotent operations

✅ TOOL ANNOTATIONS
  □ Set readOnlyHint: true for read-only tools
  □ Set destructiveHint: true for destructive tools
  □ Set idempotentHint: true for safely retryable tools
  □ Use clear, positive descriptions (say what it does, not what it doesn't)

✅ DATA PROTECTION
  □ Never return raw PII in tool results
  □ Minimize parameters — only request what's functionally needed
  □ Don't request demographic or health data via parameters
  □ Sanitize user-generated content before returning

✅ OPERATIONAL SECURITY
  □ Implement rate limiting on tool execution
  □ Log all tool invocations for audit trails
  □ Update UI to reflect agent-initiated state changes
  □ Handle errors gracefully — never expose internal details
```

---

## Detailed Checklist

### 1. Validate All Input in `execute()`

**Why**: Agent-provided parameters may be manipulated by prompt injection or may not match expected types/ranges.

```js
navigator.modelContext.registerTool({
  name: "transfer-funds",
  description: "Transfer funds between accounts",
  inputSchema: {
    type: "object",
    properties: {
      fromAccount: { type: "string" },
      toAccount: { type: "string" },
      amount: { type: "number", minimum: 0.01 }
    },
    required: ["fromAccount", "toAccount", "amount"]
  },
  execute: async ({ fromAccount, toAccount, amount }) => {
    // ✅ VALIDATE beyond JSON Schema
    if (typeof amount !== 'number' || amount <= 0 || amount > 10000) {
      return { error: "Invalid amount" };
    }
    if (!isValidAccountId(fromAccount) || !isValidAccountId(toAccount)) {
      return { error: "Invalid account" };
    }
    if (fromAccount === toAccount) {
      return { error: "Cannot transfer to same account" };
    }
    
    // ✅ Additional business logic validation
    const balance = await getBalance(fromAccount);
    if (amount > balance) {
      return { error: "Insufficient funds" };
    }
    
    // Proceed with validated parameters
    await processTransfer(fromAccount, toAccount, amount);
    return { status: "completed", amount };
  }
});
```

### 2. Use `requestUserInteraction()` for Destructive Actions

**Why**: Destructive, irreversible, or high-value actions should always require explicit user confirmation. This prevents prompt injection attacks from triggering purchases, deletions, or account changes.

```js
navigator.modelContext.registerTool({
  name: "delete-account",
  description: "Permanently delete the user's account and all associated data",
  annotations: {
    destructiveHint: true,
    readOnlyHint: false,
    idempotentHint: false
  },
  execute: async (params, agent) => {
    // ✅ ALWAYS require user confirmation for destructive actions
    const confirmed = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const dialog = document.getElementById("delete-confirmation");
        dialog.showModal();
        
        dialog.querySelector("#confirm-delete").onclick = () => {
          dialog.close();
          resolve(true);
        };
        dialog.querySelector("#cancel-delete").onclick = () => {
          dialog.close();
          resolve(false);
        };
      });
    });
    
    if (!confirmed) {
      return { status: "cancelled", message: "Account deletion cancelled by user" };
    }
    
    await deleteUserAccount();
    return { status: "deleted" };
  }
});
```

**Actions requiring `requestUserInteraction()`**:
- Purchases and payments
- Account deletion or modification
- Data sharing with third parties
- Sending messages or emails
- Financial transactions
- Content publication
- Permission changes

### 3. Check `event.agentInvoked`

**Why**: Differentiating between agent-initiated and human-initiated actions allows conditional handling — applying additional safeguards when an agent submits forms or triggers events.

```js
document.querySelector("#checkout-form").addEventListener("submit", (event) => {
  if (event.agentInvoked) {
    // ✅ Agent-triggered: apply additional safeguards
    event.preventDefault();
    
    // Show confirmation dialog
    event.respondWith(async () => {
      const confirmed = await showCheckoutConfirmation();
      if (confirmed) {
        await processCheckout();
        return { status: "completed" };
      }
      return { status: "cancelled" };
    });
  } else {
    // Human-triggered: normal flow (user already clicked the button)
    processCheckout();
  }
});
```

### 4. Set `readOnlyHint` for Read-Only Tools

**Why**: Tells agents and browsers that this tool has no side effects, enabling optimizations (concurrent execution) and reducing the need for user confirmation.

```js
// ✅ Read-only tool — safe to call without confirmation
navigator.modelContext.registerTool({
  name: "get-account-balance",
  description: "Retrieve the current account balance",
  annotations: {
    readOnlyHint: true,
    destructiveHint: false,
    idempotentHint: true
  },
  execute: async ({ accountId }) => {
    const balance = await fetchBalance(accountId);
    return { balance: balance.formatted, currency: balance.currency };
  }
});
```

> **MiguelsPizza**: "The browser could allow concurrent execution of tools marked as `readOnly` while serializing those marked `destructive`."

### 5. Never Return Raw PII in Tool Results

**Why**: Tool return values are processed by the agent's language model. PII in tool outputs could be exfiltrated through subsequent tool calls or exposed in conversation history.

```js
navigator.modelContext.registerTool({
  name: "get-user-profile",
  description: "Get user profile information",
  execute: async () => {
    const user = await fetchUserProfile();
    
    // ❌ BAD: Returning raw PII
    // return { name: user.name, email: user.email, ssn: user.ssn };
    
    // ✅ GOOD: Return only necessary, non-sensitive data
    return {
      displayName: user.displayName,
      memberSince: user.memberSince,
      preferredLanguage: user.preferredLanguage
      // No email, no SSN, no phone number
    };
  }
});
```

### 6. Implement Rate Limiting

**Why**: Prevents abuse through rapid automated tool invocations. A compromised agent could call tools at machine speed.

```js
const rateLimiter = {
  calls: new Map(),
  maxCalls: 10,       // max calls per window
  windowMs: 60_000,   // 1-minute window
  
  check(toolName) {
    const now = Date.now();
    const calls = this.calls.get(toolName) || [];
    const recent = calls.filter(t => now - t < this.windowMs);
    
    if (recent.length >= this.maxCalls) {
      return false;  // Rate limited
    }
    
    recent.push(now);
    this.calls.set(toolName, recent);
    return true;
  }
};

navigator.modelContext.registerTool({
  name: "send-message",
  description: "Send a message to another user",
  execute: async ({ recipient, message }) => {
    // ✅ Rate limit before execution
    if (!rateLimiter.check("send-message")) {
      return { error: "Rate limit exceeded. Please try again later." };
    }
    
    await sendMessage(recipient, message);
    return { status: "sent" };
  }
});
```

### 7. Use Positive, Clear Descriptions

**Why**: Ambiguous or negative descriptions can be misinterpreted by agents. Positive descriptions clearly state what the tool does, reducing misrepresentation risk.

```js
// ❌ BAD: Ambiguous
{
  name: "finalizeCart",
  description: "Finalizes the current shopping cart"
}

// ✅ GOOD: Clear and specific
{
  name: "purchase-cart-items",
  description: "Completes the purchase of all items currently in the shopping cart. Charges the user's default payment method. This action is irreversible."
}

// ❌ BAD: Negative description
{
  name: "process-data",
  description: "Processes data. Does not delete anything."
}

// ✅ GOOD: Positive description
{
  name: "analyze-sales-report",
  description: "Reads the current month's sales data and returns a summary with totals, averages, and trends. Read-only operation."
}
```

### 8. Log All Tool Invocations

**Why**: Audit trails enable detection of suspicious patterns, debugging of issues, and compliance with security policies.

```js
async function logToolInvocation(toolName, params, result, agentId) {
  await fetch("/api/audit-log", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      tool: toolName,
      parameters: sanitizeForLog(params),   // Remove sensitive data
      result: result.status || "completed",
      agentId: agentId,
      timestamp: new Date().toISOString(),
      userSession: getCurrentSessionId()
    })
  });
}

navigator.modelContext.registerTool({
  name: "update-settings",
  description: "Update user account settings",
  execute: async (params, agent) => {
    const result = await updateSettings(params);
    
    // ✅ Log the invocation
    await logToolInvocation("update-settings", params, result, agent.id);
    
    return result;
  }
});
```

### 9. Update UI to Reflect State Changes

**Why**: Users need visibility into what agents are doing. UI should reflect all state changes, whether initiated by human interaction or agent tool calls.

```js
navigator.modelContext.registerTool({
  name: "add-to-cart",
  description: "Add a product to the shopping cart",
  execute: async ({ productId, quantity }) => {
    const result = await addToCart(productId, quantity);
    
    // ✅ Update UI so user sees what happened
    updateCartBadge(result.cartCount);
    showNotification(`Agent added ${result.productName} to your cart`);
    refreshCartSidebar();
    
    return { 
      status: "added",
      product: result.productName,
      cartTotal: result.cartCount
    };
  }
});
```

### 10. Use `toolautosubmit` Only for Idempotent Operations

**Why**: `toolautosubmit` allows agents to auto-submit forms without user interaction. This should only be used for operations that are safe to retry and have no side effects.

```html
<!-- ✅ GOOD: Idempotent search operation -->
<form toolautosubmit>
  <input name="query" type="text" />
  <button type="submit">Search</button>
</form>

<!-- ❌ BAD: Non-idempotent purchase operation -->
<!-- <form toolautosubmit>
  <input name="card" type="text" />
  <button type="submit">Buy Now</button>
</form> -->

<!-- ✅ GOOD: Non-idempotent operation WITHOUT toolautosubmit -->
<form>
  <input name="card" type="text" />
  <button type="submit">Buy Now</button>
</form>
```

---

## Security Anti-Patterns to Avoid

| Anti-Pattern | Risk | Alternative |
|-------------|------|-------------|
| Trusting agent-provided parameters without validation | Injection, data corruption | Validate all inputs in `execute()` |
| Returning raw PII in tool results | Data exfiltration | Redact sensitive fields |
| Ambiguous tool descriptions | Misrepresentation attacks | Use clear, positive descriptions |
| No rate limiting | Automated abuse | Implement per-tool rate limits |
| No user confirmation for destructive ops | Unauthorized actions | Use `requestUserInteraction()` |
| Exposing internal error details | Information leakage | Return generic error messages |
| Over-parameterized tools | Privacy fingerprinting | Request only necessary params |
| No audit logging | Undetectable abuse | Log all invocations |

---

## See Also

- [Threat model overview](threat-model-overview.md)
- [Prompt injection attacks](prompt-injection.md)
- [Tool poisoning attacks](tool-poisoning.md)
- [Output injection attacks](output-injection.md)
- [Misrepresentation of intent](misrepresentation.md)
- [Privacy leakage](privacy-leakage.md)
- [User gesture activation](user-gesture-activation.md)
- [External security guides](external-security-guides.md)
- [requestUserInteraction() API](../06-user-interaction/request-user-interaction.md)
- [agentInvoked flag](../06-user-interaction/agent-invoked-flag.md)
- [Human-in-the-loop philosophy](../06-user-interaction/human-in-the-loop.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [ToolAnnotations metadata](../02-specification/annotations.md)

## References

- [Issue #11](https://github.com/webmachinelearning/webmcp/issues/11) — Prompt injection attacks
- [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) — Action-specific permissions
- [Issue #53](https://github.com/webmachinelearning/webmcp/issues/53) — Tool annotations
- [Issue #62](https://github.com/webmachinelearning/webmcp/issues/62) — User gesture activation
- [W3C TAG Security and Privacy Questionnaire](https://w3ctag.github.io/security-questionnaire/)
