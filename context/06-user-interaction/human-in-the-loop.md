# Human-in-the-Loop Philosophy

> WebMCP's core design principle — the user maintains visibility and control while agents augment rather than replace human decision-making.

## Status

- **Issues**: [#21](https://github.com/webmachinelearning/webmcp/issues/21), [#20](https://github.com/webmachinelearning/webmcp/issues/20), [#44](https://github.com/webmachinelearning/webmcp/issues/44)
- **Resolution (Sep 2025)**: "Human-in-the-loop first, automation deferred."
- **Key Contributors**: Brandon Walderman (Microsoft), Khushal Sagar (Google), Ilya Grigorik (Shopify)

---

## Core Principle

WebMCP is designed around a fundamental philosophy: **the user maintains visibility and control** over what AI agents do on their behalf. The agent augments the user's capabilities but does not replace human decision-making for consequential actions.

```
┌───────────────────────────────────────────────────┐
│              HUMAN-IN-THE-LOOP MODEL               │
│                                                    │
│  ┌──────┐    ┌────────┐    ┌──────┐    ┌───────┐  │
│  │ User │───▶│ Agent  │───▶│ Tool │───▶│ Site  │  │
│  │      │◀───│        │◀───│      │◀───│       │  │
│  └──────┘    └────────┘    └──────┘    └───────┘  │
│     ▲                          │                   │
│     │    requestUserInteraction│                   │
│     └──────────────────────────┘                   │
│                                                    │
│  User is ALWAYS in the loop:                       │
│  • Can see what agent is doing                     │
│  • Can approve/reject actions                      │
│  • Can take over at any point                      │
│  • Can block abusive sites                         │
└───────────────────────────────────────────────────┘
```

### Resolution: Human-in-the-Loop First

The September 2025 resolution established the project's direction:

> **"Human-in-the-loop first, automation deferred."**

This means:
1. The initial WebMCP API prioritizes user involvement
2. Fully autonomous operation is a future consideration, not a launch feature
3. Every destructive or consequential action should involve the user
4. Automation can be layered on top once safety patterns are proven

---

## Design Principles

### 1. User Maintains Visibility

The user should always be able to see what the agent is doing:

```js
navigator.modelContext.registerTool({
  name: "update-profile",
  description: "Update user profile information",
  execute: async (params, agent) => {
    // ✅ Update UI so user sees the change
    showNotification(`Agent is updating your profile: ${params.field}`);
    updateProfilePreview(params);
    
    const result = await saveProfile(params);
    
    // ✅ Confirm the change visually
    showNotification(`Profile updated: ${params.field} changed successfully`);
    refreshProfileDisplay();
    
    return result;
  }
});
```

### 2. User Maintains Control

The user can approve, reject, or modify agent actions:

```js
navigator.modelContext.registerTool({
  name: "send-email",
  description: "Send an email on behalf of the user",
  execute: async (params, agent) => {
    // ✅ User reviews and approves before sending
    const approval = await agent.requestUserInteraction(async () => {
      return await new Promise((resolve) => {
        const preview = document.getElementById("email-preview");
        preview.querySelector(".to").textContent = params.to;
        preview.querySelector(".subject").textContent = params.subject;
        preview.querySelector(".body").textContent = params.body;
        preview.style.display = "block";
        
        // User can edit the email before approving
        document.getElementById("send-btn").onclick = () => {
          const edited = {
            to: preview.querySelector(".to").textContent,
            subject: preview.querySelector(".subject").textContent,
            body: preview.querySelector(".body").textContent
          };
          resolve({ approved: true, params: edited });
        };
        
        document.getElementById("cancel-btn").onclick = () => {
          resolve({ approved: false });
        };
      });
    });
    
    if (!approval.approved) {
      return { status: "cancelled" };
    }
    
    await sendEmail(approval.params);
    return { status: "sent" };
  }
});
```

### 3. Cooperative Scenarios

WebMCP enables cooperative workflows where agents and users work together:

```js
// Agent handles the tedious parts, user handles the decisions
navigator.modelContext.registerTool({
  name: "plan-trip",
  description: "Research and plan a trip itinerary",
  execute: async (params, agent) => {
    // Agent does the research (autonomous)
    const flights = await searchFlights(params.destination, params.dates);
    const hotels = await searchHotels(params.destination, params.dates);
    const activities = await suggestActivities(params.destination);
    
    // User makes the choices (human-in-the-loop)
    const selectedFlight = await agent.requestUserInteraction(async () => {
      return await showFlightPicker(flights);
    });
    
    const selectedHotel = await agent.requestUserInteraction(async () => {
      return await showHotelPicker(hotels);
    });
    
    const selectedActivities = await agent.requestUserInteraction(async () => {
      return await showActivitySelector(activities);
    });
    
    // Agent assembles the itinerary (autonomous)
    const itinerary = buildItinerary(selectedFlight, selectedHotel, selectedActivities);
    
    return { itinerary };
  }
});
```

### 4. Agent Augments, Not Replaces

The agent handles tasks that are:
- Repetitive or tedious (searching, comparing, filling forms)
- Data-intensive (aggregating information across sites)
- Time-consuming (monitoring, waiting for responses)

The user handles decisions that are:
- Consequential (purchases, deletions, sharing)
- Subjective (preferences, style choices)
- Legally significant (agreements, contracts, compliance)
- Sensitive (personal data, financial decisions)

---

## Commerce Perspective: Ilya Grigorik (Shopify)

Ilya Grigorik contributed a key insight from the commerce domain:

> "We need to have a path where a tool can signal to autonomous workflow that user input or review is required. With my commerce hat on, this is often due to terms, disclosures, liability & compliance requirements. The agent can build a cart and prefill the information, but then the tool needs a mechanism to indicate that hand-off is required."

### Commerce Human-in-the-Loop Requirements

| Stage | Agent Can Do | User Must Do |
|-------|-------------|-------------|
| **Product Search** | Search, filter, compare | Select preferred item |
| **Cart Management** | Add items, apply coupons | Review final cart |
| **Checkout** | Prefill address, select shipping | Confirm purchase |
| **Payment** | Select saved payment method | Authorize payment |
| **Terms** | Display terms | Accept/decline terms |
| **Post-Purchase** | Track order | Approve returns/refunds |

---

## Trust Model

### Three-Party Responsibility

The human-in-the-loop model distributes responsibility:

| Party | Responsibility |
|-------|---------------|
| **Website** | Implement `requestUserInteraction()` for consequential actions; provide clear UI for user review; use accurate tool descriptions |
| **Agent** | Request user confirmation before destructive actions; present tool effects clearly; respect user decisions |
| **Browser** | Mediate between agent and user; provide block/allow controls; show trust indicators |

### Progressive Trust

Over time, users may grant more autonomy to trusted sites:

```
First visit:     Agent requests confirmation for every action
Repeat visits:   "Always allow" for specific low-risk tools
Trusted sites:   User grants broader automation permissions
```

This progression is governed by:
- Action-specific permissions (Issue #44)
- Browser-managed "Always allow" with TTL
- User's explicit choice to increase automation level

---

## Relationship to Automation

### Current State: Human-in-the-Loop Required

WebMCP v1 requires human involvement for consequential actions. The `requestUserInteraction()` API is the primary mechanism.

### Future: Automation as an Opt-In Layer

Future iterations may support fully autonomous operation, but only as an explicit user choice:
- User explicitly grants "auto-approve" for specific tools on specific sites
- Browser enforces limits on auto-approved actions
- Audit trails log all autonomous actions for user review
- Users can revoke auto-approval at any time

### toolautosubmit: Controlled Automation

The `toolautosubmit` attribute on forms allows agents to auto-submit without user interaction, but only for explicitly marked forms:

```html
<!-- ✅ Search forms: safe to auto-submit -->
<form toolautosubmit>
  <input name="query" type="text" />
  <button type="submit">Search</button>
</form>

<!-- ❌ Purchase forms: NEVER auto-submit -->
<form>
  <input name="card" type="text" />
  <button type="submit">Buy Now</button>
</form>
```

---

## See Also

- [requestUserInteraction() API](request-user-interaction.md)
- [agentInvoked flag](agent-invoked-flag.md)
- [User takeover during execution](user-takeover.md)
- [Elicitation design patterns](elicitation-patterns.md)
- [Misrepresentation of intent](../04-security/misrepresentation.md)
- [Developer security checklist](../04-security/developer-security-checklist.md)
- [Security threat model](../04-security/threat-model-overview.md)
- [User gesture activation](../04-security/user-gesture-activation.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [ToolAnnotations metadata](../02-specification/annotations.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)
- [Creative design use cases](../11-use-cases/creative-design.md)

## References

- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #20](https://github.com/webmachinelearning/webmcp/issues/20) — Interleaving user and agent interaction
- [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) — Action-specific permissions
- [Issue #53](https://github.com/webmachinelearning/webmcp/issues/53) — Tool annotations & side-effect hints
