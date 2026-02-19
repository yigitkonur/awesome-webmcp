# Declarative API Tutorial

> A step-by-step guide to WebMCP's declarative API — turning ordinary HTML forms into structured tools using just HTML attributes, with no JavaScript required.

---

## Overview

The declarative API is the fastest path to making your site agent-accessible. By adding a few attributes to existing `<form>` elements, the browser automatically generates tool schemas from your form structure.

From the Chrome early preview documentation:

> "You can annotate an ordinary `<form>` with `toolname`, `tooldescription`, and optionally `toolautosubmit`… and the browser will translate the form fields into a structured tool schema that agents can use."

---

## Core Attributes

| Attribute | Element | Required | Description |
|-----------|---------|----------|-------------|
| `toolname` | `<form>` | Yes | The tool's identifier (used by agents to invoke it) |
| `tooldescription` | `<form>` | Yes | What the tool does (natural language) |
| `toolparamdescription` | `<input>`, `<select>`, `<textarea>` | Recommended | Description of each parameter |
| `toolautosubmit` | `<form>` | No | Allow agent to submit without user click |

> **Important**: Both `toolname` AND `tooldescription` must be present on the form. If only `toolname` is set without `tooldescription`, the form will not be recognized as a tool.

---

## Step 1: Start with an Existing Form

Take any HTML form you already have:

```html
<form method="post" action="/api/subscribe">
  <label for="email">Email</label>
  <input type="email" id="email" name="email" required>

  <label for="plan">Plan</label>
  <select id="plan" name="plan">
    <option value="free">Free</option>
    <option value="pro">Pro</option>
    <option value="enterprise">Enterprise</option>
  </select>

  <button type="submit">Subscribe</button>
</form>
```

---

## Step 2: Add toolname and tooldescription

Add the two required attributes to the `<form>` element:

```html
<form toolname="subscribe"
      tooldescription="Subscribe to the service with the selected plan"
      method="post"
      action="/api/subscribe">
  <!-- ... form fields unchanged ... -->
</form>
```

That's it — the browser now exposes this form as a tool called "subscribe" to any connected agent.

---

## Step 3: Add toolparamdescription to Inputs

Provide descriptions for each input to help agents understand what values to provide:

```html
<form toolname="subscribe"
      tooldescription="Subscribe to the service with the selected plan"
      method="post"
      action="/api/subscribe">

  <label for="email">Email</label>
  <input type="email"
         id="email"
         name="email"
         toolparamdescription="The email address to create the subscription for"
         required>

  <label for="plan">Plan</label>
  <select id="plan"
          name="plan"
          toolparamdescription="Subscription plan tier (Free, Pro, or Enterprise)">
    <option value="free">Free</option>
    <option value="pro">Pro</option>
    <option value="enterprise">Enterprise</option>
  </select>

  <button type="submit">Subscribe</button>
</form>
```

### How the Browser Generates the Schema

The browser reads your form and auto-generates a JSON Schema equivalent:

```json
{
  "name": "subscribe",
  "description": "Subscribe to the service with the selected plan",
  "inputSchema": {
    "type": "object",
    "properties": {
      "email": {
        "type": "string",
        "description": "The email address to create the subscription for"
      },
      "plan": {
        "type": "string",
        "enum": ["free", "pro", "enterprise"],
        "description": "Subscription plan tier (Free, Pro, or Enterprise)"
      }
    },
    "required": ["email"]
  }
}
```

The browser infers:
- **Parameter names** from the `name` attribute
- **Types** from the `type` attribute (`email` → string, `number` → number)
- **Required** from the `required` attribute
- **Enum values** from `<select>` options
- **Descriptions** from `toolparamdescription`

---

## Step 4: Understanding Agent Invocation Flow

When an agent invokes a declarative tool:

1. **Agent sends tool call** with parameters (e.g., `{ email: "user@example.com", plan: "pro" }`)
2. **Browser pre-fills the form** — sets each field to the provided value
3. **Browser highlights the form** — applies `:tool-form-active` pseudo-class
4. **User reviews** — the user can see what the agent filled in
5. **User submits** — the user clicks the submit button (unless `toolautosubmit` is set)
6. **Form submits normally** — the form's `action` and `method` are used
7. **Result returned to agent** — via `respondWith()` if handled in JavaScript

---

## Step 5: Add toolautosubmit for Safe Operations

For operations that are low-risk and reversible, add `toolautosubmit`:

```html
<!-- Safe: newsletter signup is reversible -->
<form toolname="newsletter-signup"
      tooldescription="Sign up for the weekly newsletter"
      toolautosubmit
      method="post"
      action="/api/newsletter">
  <input type="email" name="email"
         toolparamdescription="Email address for newsletter delivery"
         required>
  <button type="submit">Sign Up</button>
</form>
```

With `toolautosubmit`, the agent can submit the form without waiting for the user to click the button. The browser still pre-fills the fields and the submission is visible to the user.

### When NOT to Use toolautosubmit

- **Payment forms** — Never auto-submit financial transactions
- **Account deletion** — Irreversible actions need confirmation
- **Data modification** — Edits to important records
- **Forms with side effects** — Anything that can't be easily undone

---

## Step 6: Handle Agent-Initiated Submissions

Use `SubmitEvent.agentInvoked` to detect and respond to agent submissions:

```html
<form toolname="search-products"
      tooldescription="Search the product catalog by keyword and category"
      toolautosubmit
      method="get"
      action="/search">

  <input type="text" name="query"
         toolparamdescription="Search keywords"
         required>

  <select name="category"
          toolparamdescription="Product category to search within">
    <option value="">All Categories</option>
    <option value="electronics">Electronics</option>
    <option value="clothing">Clothing</option>
    <option value="books">Books</option>
  </select>

  <button type="submit">Search</button>
</form>

<script>
document.querySelector('form[toolname="search-products"]').addEventListener('submit', (e) => {
  if (e.agentInvoked) {
    e.preventDefault();

    const formData = new FormData(e.target);
    const query = formData.get('query');
    const category = formData.get('category');

    // Return structured results to the agent
    e.respondWith(
      fetch(`/api/search?q=${encodeURIComponent(query)}&cat=${category}`)
        .then(r => r.json())
        .then(results => ({
          content: [{
            type: "text",
            text: JSON.stringify({
              total: results.length,
              items: results.slice(0, 10)
            })
          }]
        }))
    );
  }
  // Human submissions proceed normally (form navigates to /search)
});
</script>
```

---

## Step 7: Add Visual Feedback with CSS

Style your forms for agent interaction:

```css
/* Default state */
form[toolname] {
  transition: border-color 0.3s, background-color 0.3s;
  border: 2px solid transparent;
}

/* Agent is filling the form */
form:tool-form-active {
  border-color: #1a73e8;
  background-color: rgba(26, 115, 232, 0.04);
  box-shadow: 0 0 0 4px rgba(26, 115, 232, 0.1);
}

/* Submit button when agent is active */
button[type="submit"]:tool-submit-active {
  background-color: #1a73e8;
  color: white;
  cursor: pointer;
}

/* Indicate which fields were filled by agent */
form:tool-form-active input:not(:placeholder-shown),
form:tool-form-active select,
form:tool-form-active textarea:not(:empty) {
  background-color: #e8f0fe;
  border-color: #1a73e8;
}
```

---

## Step 8: Listen for Tool Events

```js
const form = document.querySelector('form[toolname="subscribe"]');

// Fired when agent has pre-filled the form fields
form.addEventListener('toolactivated', (e) => {
  console.log('Agent filled the form');
  // Show a review banner
  document.getElementById('agent-banner').style.display = 'block';
  document.getElementById('agent-banner').textContent =
    '🤖 An AI agent has filled this form. Please review before submitting.';
});

// Fired when user cancels the agent's form fill
form.addEventListener('toolcancel', (e) => {
  console.log('Agent form fill was cancelled');
  document.getElementById('agent-banner').style.display = 'none';
});
```

---

## Complete Example: Multi-Form Page

A page with multiple declarative tools:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WebMCP Declarative Tools</title>
  <style>
    body { font-family: system-ui; max-width: 700px; margin: 2rem auto; padding: 0 1rem; }
    form { border: 1px solid #e0e0e0; padding: 1.5rem; border-radius: 8px; margin: 1.5rem 0; }
    form:tool-form-active { border-color: #1a73e8; background: #f8f9ff; }
    h2 { margin-top: 0; }
    label { display: block; margin: 0.75rem 0 0.25rem; font-weight: 600; }
    input, select, textarea { width: 100%; padding: 0.5rem; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
    button { margin-top: 1rem; padding: 0.5rem 1.5rem; background: #1a73e8; color: white; border: none; border-radius: 4px; cursor: pointer; }
  </style>
</head>
<body>
  <h1>📋 Declarative WebMCP Demo</h1>

  <!-- Tool 1: Search (safe, auto-submit OK) -->
  <form toolname="search-articles"
        tooldescription="Search published articles by keyword, author, or topic"
        toolautosubmit
        method="get"
        action="/search">
    <h2>Search Articles</h2>
    <label for="q">Keywords</label>
    <input type="text" id="q" name="query"
           toolparamdescription="Search keywords or phrase" required>

    <label for="author">Author</label>
    <input type="text" id="author" name="author"
           toolparamdescription="Filter by author name">

    <label for="topic">Topic</label>
    <select id="topic" name="topic"
            toolparamdescription="Article topic category">
      <option value="">All Topics</option>
      <option value="tech">Technology</option>
      <option value="science">Science</option>
      <option value="business">Business</option>
    </select>

    <button type="submit">Search</button>
  </form>

  <!-- Tool 2: Contact (needs review, no auto-submit) -->
  <form toolname="contact-author"
        tooldescription="Send a message to an article author"
        method="post"
        action="/api/contact">
    <h2>Contact Author</h2>
    <label for="to">Author</label>
    <input type="text" id="to" name="author_name"
           toolparamdescription="Name of the author to contact" required>

    <label for="msg">Message</label>
    <textarea id="msg" name="message" rows="4"
              toolparamdescription="Your message to the author" required></textarea>

    <button type="submit">Send Message</button>
  </form>

  <!-- Tool 3: Newsletter (safe, auto-submit OK) -->
  <form toolname="subscribe-newsletter"
        tooldescription="Subscribe to the weekly digest newsletter"
        toolautosubmit
        method="post"
        action="/api/subscribe">
    <h2>Newsletter</h2>
    <label for="sub-email">Email</label>
    <input type="email" id="sub-email" name="email"
           toolparamdescription="Email address for newsletter delivery" required>
    <button type="submit">Subscribe</button>
  </form>
</body>
</html>
```

---

## Testing Declarative Tools

1. Open your page in Chrome Canary with WebMCP flag enabled
2. Open the Model Context Tool Inspector extension
3. All three declarative tools should appear:
   - `search-articles` (with ⚡ autosubmit indicator)
   - `contact-author` (manual submit required)
   - `subscribe-newsletter` (with ⚡ autosubmit indicator)
4. Click a tool and enter test parameters
5. Watch the form fields fill in and the `:tool-form-active` styles apply

---

## Declarative vs Imperative: When to Use Each

| Scenario | Declarative | Imperative |
|----------|-------------|------------|
| Standard HTML form | ✅ Best fit | Overkill |
| Complex dynamic logic | ❌ Limited | ✅ Full control |
| No JavaScript needed | ✅ HTML only | Requires JS |
| Custom error handling | Limited | ✅ Full control |
| State-dependent tools | ❌ Static | ✅ Dynamic |
| Multiple tools from one form | ❌ One per form | ✅ Unlimited |
| Pre-existing forms | ✅ Add 2 attributes | Requires wrapping |

You can mix both APIs on the same page — declarative for simple forms, imperative for complex logic.

---

## See Also

- [Imperative API Tutorial](./imperative-api-tutorial.md) — JavaScript-based tools
- [Getting Started](./getting-started.md) — Quick start guide
- [Form Filling Use Case](../11-use-cases/form-filling.md) — Real-world form examples
- [Best Practices](./best-practices.md) — Tool description guidelines
- [Testing Tools](./testing-tools.md) — Testing declarative tools
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Declarative API overview](../03-declarative-api/overview.md) — Declarative API overview
- [toolname attribute](../03-declarative-api/toolname-attribute.md) — toolname attribute
- [Form mapping](../03-declarative-api/form-mapping.md) — Form mapping
- [Auto-submit attribute](../03-declarative-api/toolautosubmit-attribute.md) — Auto-submit attribute
- [Imperative vs declarative comparison](../03-declarative-api/imperative-vs-declarative.md) — Imperative vs declarative comparison
- [Declarative API design debates](../13-spec-discussions/declarative-api-debates.md) — Declarative API design debates
- [Security checklist](../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../15-reference/api-reference.md) — Complete API reference
