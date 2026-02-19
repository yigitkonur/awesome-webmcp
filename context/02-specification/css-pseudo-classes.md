# CSS Pseudo-Classes for Agent State

## Overview

WebMCP introduces CSS pseudo-classes that allow web developers to style their pages
differently when an AI agent is interacting with page elements (see [spec](https://webmachinelearning.github.io/webmcp),
[Chromium implementation](https://chromium.googlesource.com/chromium/src/+/a6dd386ea2457906e4566eb6c7b6ca031194c1ad)). These pseudo-classes
enable adaptive UI that responds to agent activity, improving the user experience by
providing visual feedback about what the agent is doing.

## Pseudo-Classes

### :tool-form-active

The `:tool-form-active` pseudo-class matches a `<form>` element when an AI agent is
actively using it — i.e., the form is being populated or interacted with as part of
a tool invocation.

```css
/* Highlight forms being used by an agent */
form:tool-form-active {
  border: 2px solid #4A90D9;
  background-color: rgba(74, 144, 217, 0.05);
  box-shadow: 0 0 10px rgba(74, 144, 217, 0.2);
}
```

#### Use Cases

- **Visual indicator** — Show users which form the agent is currently operating on
- **Disable human input** — Prevent conflicts while agent is filling the form
- **Loading states** — Display progress indicators on forms being processed
- **Accessibility** — Screen readers can announce agent activity on forms (see [Issue #65](https://github.com/webmachinelearning/webmcp/issues/65))

#### Example: Agent Activity Indicator

```css
/* Base form style */
form {
  border: 1px solid #ccc;
  padding: 1rem;
  transition: all 0.3s ease;
}

/* When agent is using the form */
form:tool-form-active {
  border-color: #4A90D9;
  position: relative;
}

/* Add "Agent Active" badge */
form:tool-form-active::before {
  content: "🤖 Agent is filling this form";
  position: absolute;
  top: -12px;
  left: 10px;
  background: #4A90D9;
  color: white;
  padding: 2px 10px;
  border-radius: 10px;
  font-size: 0.75rem;
  font-weight: bold;
}

/* Dim inputs while agent is working */
form:tool-form-active input,
form:tool-form-active select,
form:tool-form-active textarea {
  opacity: 0.7;
  pointer-events: none;
}
```

### :tool-submit-active

The `:tool-submit-active` pseudo-class matches a submit button when it is active
during an agent-initiated form submission.

```css
/* Style submit button during agent operation */
button[type="submit"]:tool-submit-active {
  background-color: #4A90D9;
  color: white;
  cursor: wait;
  position: relative;
}
```

#### Use Cases

- **Loading spinner** — Show that submission is in progress
- **Prevent double-submit** — Visually indicate the button is already engaged
- **Agent branding** — Distinguish agent submissions from human clicks

#### Example: Animated Submit Button

```css
/* Normal submit button */
button[type="submit"] {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  transition: all 0.3s ease;
}

/* During agent submission */
button[type="submit"]:tool-submit-active {
  background-color: #4A90D9;
  cursor: wait;
  pointer-events: none;
}

/* Add spinning animation */
button[type="submit"]:tool-submit-active::after {
  content: "";
  display: inline-block;
  width: 14px;
  height: 14px;
  margin-left: 8px;
  border: 2px solid transparent;
  border-top-color: white;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

## Styling Patterns

### Pattern 1: Full Agent Mode

Restyle the entire page when an agent is operating:

```css
/* When any form on the page is being used by an agent */
body:has(form:tool-form-active) {
  /* Dim the background */
  --page-opacity: 0.8;
}

body:has(form:tool-form-active) .sidebar {
  opacity: var(--page-opacity);
  pointer-events: none;
}

body:has(form:tool-form-active) .agent-status-bar {
  display: flex;
}
```

### Pattern 2: Graceful Degradation

Styles work even if the browser doesn't support WebMCP pseudo-classes:

```css
/* Base styles (always applied) */
form {
  border: 1px solid #ddd;
  padding: 1rem;
}

/* Agent-specific styles (ignored by browsers without support) */
@supports selector(:tool-form-active) {
  form:tool-form-active {
    border-color: #4A90D9;
    background: linear-gradient(135deg, #f0f7ff 0%, #ffffff 100%);
  }
}
```

### Pattern 3: Accessibility-Focused

Ensure agent states are accessible to screen readers and keyboard users:

```css
/* High contrast for agent-active forms */
@media (prefers-contrast: high) {
  form:tool-form-active {
    border: 3px solid #0000ff;
    outline: 2px dashed #ff0000;
    outline-offset: 4px;
  }
}

/* Respect reduced motion preferences */
@media (prefers-reduced-motion: reduce) {
  button[type="submit"]:tool-submit-active::after {
    animation: none;
    content: "⏳";
  }
}

/* Focus styles for agent-active elements */
form:tool-form-active:focus-within {
  outline: 3px solid #4A90D9;
  outline-offset: 2px;
}
```

### Pattern 4: Dark Mode Support

```css
/* Light mode */
form:tool-form-active {
  background-color: rgba(74, 144, 217, 0.05);
  border-color: #4A90D9;
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  form:tool-form-active {
    background-color: rgba(74, 144, 217, 0.15);
    border-color: #6AB0FF;
  }

  button[type="submit"]:tool-submit-active {
    background-color: #2A6FB5;
  }
}
```

## Combining with JavaScript Events

CSS pseudo-classes complement the JavaScript event system:

```html
<style>
  form:tool-form-active {
    border: 2px solid #4A90D9;
    transition: border-color 0.3s;
  }

  .agent-overlay {
    display: none;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(74, 144, 217, 0.1);
    pointer-events: none;
    z-index: 10;
  }

  form:tool-form-active .agent-overlay {
    display: block;
  }
</style>

<form id="stampForm">
  <div class="agent-overlay">
    <span>🤖 Agent is working...</span>
  </div>
  <input type="text" name="stampName" placeholder="Stamp name">
  <input type="number" name="stampYear" placeholder="Year">
  <button type="submit">Add Stamp</button>
</form>
```

## Relationship to Declarative API

The CSS pseudo-classes are closely related to the declarative API discussion in
[Issue #22](https://github.com/webmachinelearning/webmcp/issues/22) and
[PR #76 — declarative explainer](https://github.com/webmachinelearning/webmcp/pull/76). When tools are
tied to HTML forms declaratively, the pseudo-classes provide the styling hooks:

```html
<!-- Declarative tool definition (future) -->
<form tool-name="add-stamp" tool-description="Add a new stamp">
  <input name="stampName" type="text" tool-param-description="Stamp name">
  <input name="stampYear" type="number" tool-param-description="Issue year">
  <button type="submit">Add Stamp</button>
</form>

<style>
  /* Style is the same whether tools are declarative or imperative */
  form:tool-form-active {
    border-color: #4A90D9;
  }
</style>
```

## Adaptive UI Example

A complete example showing how an e-commerce page adapts when agents are active:

```css
/* Normal state */
.product-form {
  max-width: 500px;
  margin: 0 auto;
}

.product-gallery {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}

/* Agent is using the form */
.product-form:tool-form-active {
  max-width: 300px;
  float: right;
  opacity: 0.9;
}

/* Expand gallery when agent is working */
body:has(.product-form:tool-form-active) .product-gallery {
  grid-template-columns: repeat(4, 1fr);
}

/* Show agent status */
.agent-indicator {
  display: none;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  background: #e8f4ff;
  border-radius: 8px;
  margin-bottom: 1rem;
}

body:has(:tool-form-active) .agent-indicator {
  display: flex;
}
```

## Browser Support Considerations

Since WebMCP and its CSS pseudo-classes are new (2026), consider (see [Dev.to overview](https://dev.to/axrisi/chromes-webmcp-early-preview-the-end-of-ai-agents-clicking-buttons-b6e)):

1. **Feature detection with `@supports`** — Check before applying agent-specific styles
2. **Progressive enhancement** — Pages should work without agent styles
3. **Graceful fallback** — Unknown pseudo-classes are ignored by browsers that don't support them

```css
/* Safe to use — browsers ignore unknown pseudo-classes */
form:tool-form-active {
  /* Only applied in browsers that support WebMCP */
}

/* Explicit feature detection */
@supports selector(:tool-form-active) {
  /* Browser supports WebMCP CSS pseudo-classes */
  .agent-features { display: block; }
}
```

## Related Issues

- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22) — Form-based tool definitions
- [Issue #65 — Accessibility](https://github.com/webmachinelearning/webmcp/issues/65) — Accessibility considerations for agent-active states
- [Issue #53 — Semantic hints for side-effects](https://github.com/webmachinelearning/webmcp/issues/53) — Agent behavior context

## See Also

- [Specification overview](spec-overview.md)
- [WebMCP events](events.md)
- [navigator.modelContext API](navigator-model-context.md)
- [Execute callback specification](execute-callback.md)
- [Declarative API overview](../03-declarative-api/overview.md)
- [Declarative API examples](../03-declarative-api/declarative-examples.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [agentInvoked flag](../06-user-interaction/agent-invoked-flag.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)
- [Creative design use cases](../11-use-cases/creative-design.md)
