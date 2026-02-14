# Elicitation & User Interaction

Issues: #21, #20, #44, #62, #50, #48

---

## requestUserInteraction (Issue #21)

This is the mechanism for tools to hand control back to the user mid-execution. Filed by Brandon Walderman in September 2025, it became one of the most thoroughly designed parts of the API.

### The Pattern

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

### Key Design Decisions

**RESOLUTION (Oct 2, 2025)**: Tool execution should be able to start/stop yielding to the user throughout its lifecycle.

**RESOLUTION (Oct 16, 2025)**: `requestUserInteraction` should give users an option to block abusive sites permanently, but throw an error to developers so legitimate sites can implement fallback behavior.

### Abuse Prevention

Khushal Sagar identified the core abuse vector:
> "The site intentionally keeps calling `requestUserInteraction` during legitimate tool calls. Say the tool is `addProduct` and the site says 'look at this offer first', requiring the user to hit a button to dismiss."

**stalgiag** proposed observable lifecycle signals:
- Browser emits yield start/end events tagged with execution ID
- Browser can rate-limit or downgrade attention during a single execution
- User affordance: "mute further prompts from this site/tool for this task"

**Brandon's safeguard**: `requestUserInteraction` is only available during an active tool call. No agent connected = no way to call this API.

---

## User Gesture Activation (Issue #62)

**Francois Beaufort** raised this with a concrete use case: controlling a video player via WebMCP where `requestPictureInPicture()` requires transient user activation.

**Johann Hofmann** was cautious:
> "I'm not sure just blanket granting scripts run by agents transient activation is the right solution. Most often activation is required because something will happen in the UI, and most agents won't have the ability to control it, e.g., accept permission prompts."

> "For `requestPictureInPicture` it might be fine to say if it was called from a WebMCP execution the user gesture requirement is waived."

**Khushal's take**: If an agent starts interacting with a site, it was triggered by the user, so user activation should be granted. But **renewing** activation without user interaction is uncertain.

This issue remains open with no resolution -- it's a case-by-case concern.

---

## Tool Abort via AbortSignal (Issue #48)

**Brandon** proposed using the standard `AbortSignal` pattern:

```js
execute(input, { signal }) {
  signal.addEventListener('abort', () => {
    // Roll back operation, reset UI
  });
}
```

The browser creates an `AbortController` per tool call and passes the signal. Parallels the `NavigateEvent.signal` pattern in the Navigation API.

**RESOLUTION (Jan 22, 2026)**: Use AbortSignal to signal cancellation.

---

## Multiple Elicitation Requests (Issue #50)

What happens when an agent triggers two tools that both need `requestUserInteraction`? No resolution yet. Depends on #51 (in-page agent API).

---

## Interleaving User and Agent Interaction (Issue #20)

The question: If a user "takes over" during agent tool execution, should the site be informed?

**RESOLUTION (Oct 16, 2025)**: No concrete use case identified. Elicitation (Issue #21) handles the site-initiated handoff case.

---

## Ilya Grigorik's Commerce Perspective

Shopify's Ilya Grigorik contributed a key insight:

> "We need to have a path where a tool can signal to autonomous workflow that user input or review is required. With my commerce hat on, this is often due to terms, disclosures, liability & compliance requirements. The agent can build a cart and prefill the information, but then the tool needs a mechanism to indicate that hand-off is required."
