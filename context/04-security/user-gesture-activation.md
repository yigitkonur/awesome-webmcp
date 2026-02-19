# User Gesture Activation

> Requiring user gesture before tool execution to prevent background abuse — Issue #62 by Francois Beaufort (Google Chrome).

## Status

- **Issue**: [#62](https://github.com/webmachinelearning/webmcp/issues/62) — User gesture activation
- **Filed By**: Francois Beaufort (Google Chrome)
- **Status**: Open — no resolution yet, case-by-case concern
- **Related**: [#21](https://github.com/webmachinelearning/webmcp/issues/21) (requestUserInteraction), [#44](https://github.com/webmachinelearning/webmcp/issues/44) (Permissions)

---

## The Problem

Many browser APIs require **transient user activation** (a recent user gesture like a click or keypress) before they can be called. When AI agents execute WebMCP tools, these tools may need to call gesture-gated APIs — but the tool execution was triggered by the agent, not directly by the user.

### Concrete Use Case: Video Player

Francois Beaufort raised this with a specific scenario: controlling a video player via WebMCP where `requestPictureInPicture()` requires transient user activation.

```js
navigator.modelContext.registerTool({
  name: "enter-pip",
  description: "Enter Picture-in-Picture mode for the current video",
  execute: async () => {
    const video = document.querySelector("video");
    // ❌ This will FAIL — no user gesture available
    await video.requestPictureInPicture();
    return { status: "entered PiP mode" };
  }
});
```

### APIs That Require User Gesture

| API | Requires Gesture | Risk If Waived |
|-----|-------------------|----------------|
| `requestPictureInPicture()` | Yes | Low — UI change only |
| `requestFullscreen()` | Yes | Medium — visual takeover |
| `Notification.requestPermission()` | Yes | High — permission grant |
| `navigator.share()` | Yes | Medium — data sharing |
| `window.open()` | Yes | High — popup abuse |
| `navigator.clipboard.writeText()` | Yes | High — clipboard hijacking |
| Payment Request API | Yes | Critical — financial |

---

## Discussion Positions

### Johann Hofmann (Chrome) — Cautious

> "I'm not sure just blanket granting scripts run by agents transient activation is the right solution. Most often activation is required because something will happen in the UI, and most agents won't have the ability to control it, e.g., accept permission prompts."

> "For `requestPictureInPicture` it might be fine to say if it was called from a WebMCP execution the user gesture requirement is waived."

Johann advocates for a **case-by-case approach** rather than blanket activation grants.

### Khushal Sagar (Google) — Pragmatic

Khushal's position: If an agent starts interacting with a site, it was triggered by the user, so user activation should be granted. But **renewing** activation without further user interaction is uncertain.

Key insight: There's a difference between:
1. **Initial activation**: User initiated the agent task → reasonable to grant
2. **Renewed activation**: Agent has been running for a while → should activation persist?

---

## Activation Patterns

### Pattern 1: Blanket Grant (Rejected)

Grant full transient activation to all WebMCP tool executions.

```js
// ❌ NOT RECOMMENDED — too permissive
navigator.modelContext.registerTool({
  name: "any-tool",
  execute: async () => {
    // Tool code runs with full user gesture privileges
    window.open("https://popup.example.com"); // Would succeed
    await navigator.clipboard.writeText("hijacked"); // Would succeed
  }
});
```

**Problem**: This opens the door to popup abuse, clipboard hijacking, and permission prompt abuse.

### Pattern 2: API-Specific Waiver

Certain low-risk APIs have their gesture requirement waived when called from WebMCP tool execution.

```js
// ✅ PROPOSED — waive gesture for specific safe APIs
navigator.modelContext.registerTool({
  name: "enter-pip",
  execute: async () => {
    const video = document.querySelector("video");
    // PiP is considered safe — gesture waived for WebMCP
    await video.requestPictureInPicture(); // ✅ Works
    return { status: "entered PiP mode" };
  }
});
```

**Johann's position**: This is acceptable for `requestPictureInPicture` but should not be a general rule.

### Pattern 3: User Interaction Gate

Use `requestUserInteraction()` to obtain a genuine user gesture before calling gesture-gated APIs.

```js
// ✅ RECOMMENDED — use requestUserInteraction for gesture-gated APIs
navigator.modelContext.registerTool({
  name: "share-content",
  description: "Share content via the Web Share API",
  execute: async (params, agent) => {
    await agent.requestUserInteraction(async () => {
      // Inside this callback, the user provides a real gesture
      const shareButton = document.getElementById("share-btn");
      return await new Promise((resolve) => {
        shareButton.onclick = async () => {
          await navigator.share({
            title: params.title,
            text: params.text,
            url: params.url
          });
          resolve(true);
        };
      });
    });
    return { status: "shared" };
  }
});
```

### Pattern 4: Activation Budget

Grant a limited "activation budget" for WebMCP tool executions that depletes over time or with use.

```
┌──────────────────────────────────────────────┐
│         ACTIVATION BUDGET MODEL               │
│                                               │
│  User triggers agent task                     │
│  → Budget: 3 gesture-gated API calls          │
│                                               │
│  Tool 1: requestPictureInPicture() → Budget: 2│
│  Tool 2: navigator.share()        → Budget: 1│
│  Tool 3: window.open()            → Budget: 0│
│  Tool 4: Notification.request()   → ❌ DENIED │
└──────────────────────────────────────────────┘
```

---

## Preventing Background Abuse

### Risk: Invisible Agent Activity

Without gesture requirements, a malicious tool could:
- Open invisible popups for ad fraud
- Hijack clipboard content silently
- Request permissions without user awareness
- Trigger fullscreen takeover

### Safeguards

1. **Visibility requirement**: Tools should only execute when the page is visible (not background tab)
2. **Focus requirement**: Page should be in foreground when calling gesture-gated APIs
3. **Timeout**: User activation should expire after a reasonable period (similar to browser's 5-second transient activation window)
4. **Audit trail**: Browser logs all gesture-gated API calls made during tool execution

```js
navigator.modelContext.registerTool({
  name: "sensitive-action",
  execute: async (params, agent) => {
    // ✅ Check if page is visible before proceeding
    if (document.visibilityState !== "visible") {
      return { error: "Action requires visible page" };
    }
    
    // ✅ Check if document has focus
    if (!document.hasFocus()) {
      return { error: "Action requires page focus" };
    }
    
    // Proceed with action
    // ...
  }
});
```

---

## Current Status

This issue remains **open with no resolution**. The consensus is that it requires case-by-case evaluation:

- **Low-risk APIs** (PiP, media controls): Gesture requirement likely waivable
- **Medium-risk APIs** (share, fullscreen): Use `requestUserInteraction()` 
- **High-risk APIs** (permissions, clipboard, popups): Always require genuine user gesture
- **Critical APIs** (payment, identity): Never waive gesture requirement

---

## See Also

- [Threat model overview](threat-model-overview.md)
- [Developer security checklist](developer-security-checklist.md)
- [Prompt injection attacks](prompt-injection.md)
- [requestUserInteraction() API](../06-user-interaction/request-user-interaction.md)
- [agentInvoked flag](../06-user-interaction/agent-invoked-flag.md)
- [Human-in-the-loop philosophy](../06-user-interaction/human-in-the-loop.md)
- [User takeover during execution](../06-user-interaction/user-takeover.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [WebMCP events](../02-specification/events.md)
- [toolautosubmit attribute](../03-declarative-api/toolautosubmit-attribute.md)

## References

- [Issue #62](https://github.com/webmachinelearning/webmcp/issues/62) — User gesture activation
- [Issue #21](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction
- [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) — Action-specific permissions
- [HTML Living Standard — Transient Activation](https://html.spec.whatwg.org/multipage/interaction.html#transient-activation)
