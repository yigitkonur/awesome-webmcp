# Getting Started with WebMCP

> A quick start guide to creating your first WebMCP tool — from enabling Chrome flags to testing with the Model Context Tool Inspector extension.

---

## Prerequisites

- **Chrome Canary 146+** (or Chrome Dev/Beta with the appropriate flag)
- Basic knowledge of HTML and JavaScript
- A local web server (or any static file hosting)

---

## Step 1: Check Browser Support

WebMCP is available behind an experimental flag. First, verify your Chrome version:

1. Open Chrome Canary
2. Navigate to `chrome://version`
3. Confirm version **146.0** or higher

### Feature Detection in Code

```js
if ("modelContext" in navigator) {
  console.log("✅ WebMCP is supported!");
} else {
  console.log("❌ WebMCP not available. Enable the flag or update Chrome.");
}
```

---

## Step 2: Enable the WebMCP Flag

1. Open Chrome Canary
2. Navigate to `chrome://flags`
3. Search for **"WebMCP for testing"**
4. Set it to **Enabled**
5. Click **Relaunch** to restart the browser

> **Note**: In some Chrome versions, the flag may be under **"Experimental Web Platform features"** instead. Try both search terms if one doesn't appear.

After relaunching, verify the flag is active:

```js
// Paste in DevTools console
console.log("modelContext" in navigator); // Should print: true
```

---

## Step 3: Install the Model Context Tool Inspector

The **Model Context Tool Inspector** is a Chrome extension for testing WebMCP tools:

1. Visit the [Chrome Web Store listing](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
2. Click **Add to Chrome**
3. The extension icon appears in your toolbar

### What the Inspector Does

- **Lists registered tools** — See all tools on the current page
- **Shows tool schemas** — View inputSchema, descriptions, and parameters
- **Execute tools manually** — Test tools with custom inputs
- **Gemini API integration** — Test tools via natural language with Gemini
- **Displays declarative tools** — Forms with `toolname` appear automatically

---

## Step 4: Create Your First Tool

Create a minimal HTML page with a WebMCP tool:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My First WebMCP Tool</title>
</head>
<body>
  <h1>WebMCP Hello World</h1>
  <div id="output">Waiting for tool call...</div>

  <script>
    if ("modelContext" in navigator) {
      navigator.modelContext.registerTool({
        name: "greet",
        description: "Greet someone by name and display the greeting on the page.",
        inputSchema: {
          type: "object",
          properties: {
            name: {
              type: "string",
              description: "The name of the person to greet"
            }
          },
          required: ["name"]
        },
        execute({ name }) {
          const greeting = `Hello, ${name}! Welcome to WebMCP.`;
          document.getElementById('output').textContent = greeting;

          return {
            content: [{
              type: "text",
              text: greeting
            }]
          };
        }
      });

      console.log("✅ 'greet' tool registered");
    }
  </script>
</body>
</html>
```

---

## Step 5: Test Your Tool

### Option A: Using the Inspector Extension

1. Open your page in Chrome Canary
2. Click the Model Context Tool Inspector extension icon
3. You should see the **"greet"** tool listed
4. Click on it to expand the schema
5. Enter a name in the input field
6. Click **Execute**
7. Check your page — the greeting should appear

### Option B: Using Chrome DevTools

1. Open DevTools (F12 or Cmd+Option+I)
2. Go to the **Console** tab
3. Test tool registration:

```js
// Check registered tools
navigator.modelContext
// The inspector extension will show registered tools
```

### Option C: Using the Live Travel Demo

Google provides a hosted demo to test WebMCP without writing any code:

1. Navigate to `https://travel-demo.bandarra.me/`
2. Open the Model Context Tool Inspector
3. You'll see a `searchFlights` tool
4. Execute it with parameters like:
   - origin: "SFO"
   - destination: "JFK"
   - date: "2026-03-15"

---

## Step 6: Add a Declarative Tool

Add a form-based tool to your page — no JavaScript needed:

```html
<form toolname="send-feedback"
      tooldescription="Send feedback about this page"
      method="post"
      action="/api/feedback">

  <label for="rating">Rating (1-5)</label>
  <input type="number"
         id="rating"
         name="rating"
         min="1" max="5"
         toolparamdescription="Rating from 1 (poor) to 5 (excellent)"
         required>

  <label for="comment">Comment</label>
  <textarea id="comment"
            name="comment"
            toolparamdescription="Optional feedback comment"></textarea>

  <button type="submit">Submit Feedback</button>
</form>
```

Reload the page and check the inspector — both the imperative `greet` tool and the declarative `send-feedback` tool should appear.

---

## Step 7: Combine Both APIs

A complete page with both imperative and declarative tools:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WebMCP Quick Start</title>
  <style>
    body { font-family: system-ui; max-width: 600px; margin: 2rem auto; }
    .output { padding: 1rem; background: #f0f0f0; border-radius: 4px; margin: 1rem 0; }
    form { border: 1px solid #ddd; padding: 1rem; border-radius: 4px; }
    form:tool-form-active { border-color: #4285f4; background: #e8f0fe; }
    label { display: block; margin-top: 0.5rem; font-weight: bold; }
    input, textarea, select { width: 100%; padding: 0.5rem; margin-top: 0.25rem; }
    button { margin-top: 1rem; padding: 0.5rem 1rem; }
  </style>
</head>
<body>
  <h1>🛠️ WebMCP Quick Start</h1>

  <!-- Imperative tool output -->
  <div class="output" id="greeting-output">
    Use the agent or inspector to call the "greet" tool.
  </div>

  <!-- Declarative tool -->
  <h2>Feedback Form</h2>
  <form toolname="send-feedback"
        tooldescription="Send feedback about this page"
        method="post"
        action="/api/feedback">

    <label for="rating">Rating</label>
    <select id="rating" name="rating"
            toolparamdescription="Overall rating">
      <option value="1">1 - Poor</option>
      <option value="2">2 - Fair</option>
      <option value="3" selected>3 - Good</option>
      <option value="4">4 - Very Good</option>
      <option value="5">5 - Excellent</option>
    </select>

    <label for="feedback">Comments</label>
    <textarea id="feedback" name="comment"
              toolparamdescription="Detailed feedback comments"
              rows="3"></textarea>

    <button type="submit">Submit</button>
  </form>

  <script>
    // Imperative tool
    if ("modelContext" in navigator) {
      navigator.modelContext.registerTool({
        name: "greet",
        description: "Greet someone by name and display on the page.",
        inputSchema: {
          type: "object",
          properties: {
            name: {
              type: "string",
              description: "Person's name to greet"
            }
          },
          required: ["name"]
        },
        execute({ name }) {
          const msg = `Hello, ${name}! 👋`;
          document.getElementById('greeting-output').textContent = msg;
          return { content: [{ type: "text", text: msg }] };
        }
      });

      console.log("WebMCP tools registered: greet (imperative), send-feedback (declarative)");
    } else {
      console.warn("WebMCP not supported. Enable chrome://flags → WebMCP for testing");
    }
  </script>
</body>
</html>
```

---

## Minimal Tool Template

Copy this template whenever you need to create a new tool:

```js
navigator.modelContext.registerTool({
  name: "tool-name",
  description: "What this tool does, in one clear sentence.",
  inputSchema: {
    type: "object",
    properties: {
      param1: {
        type: "string",
        description: "What this parameter is for"
      }
    },
    required: ["param1"]
  },
  execute({ param1 }) {
    // Your logic here
    // Update UI if needed

    return {
      content: [{
        type: "text",
        text: "Result message for the agent"
      }]
    };
  }
});
```

---

## Common Issues

| Issue | Solution |
|-------|----------|
| `navigator.modelContext` is undefined | Enable the Chrome flag and relaunch |
| Tool doesn't appear in inspector | Check for JavaScript errors in console |
| Declarative form not detected | Ensure `toolname` AND `tooldescription` are both set |
| Tool executes but UI doesn't update | Call your render/update functions inside `execute` |
| Extension not showing tools | Refresh the page after installing the extension |

---

## Next Steps

- **[Imperative API Tutorial](./imperative-api-tutorial.md)** — Deep dive into JavaScript tools
- **[Declarative API Tutorial](./declarative-api-tutorial.md)** — Master form-based tools
- **[Chrome Setup Guide](./chrome-setup.md)** — Detailed browser configuration
- **[Testing Tools](./testing-tools.md)** — Advanced testing patterns
- **[Best Practices](./best-practices.md)** — Write production-quality tools

---

## See Also

- [What Is WebMCP](../01-overview/what-is-webmcp.md) — Core concepts
- [Developer Tooling Use Case](../11-use-cases/developer-tooling.md) — The stamp database example
- [Travel Booking Use Case](../11-use-cases/travel-booking.md) — Official Chrome travel demo
- [Tool registration API](../02-specification/register-tool.md) — Tool registration API
- [Navigator model context interface](../02-specification/navigator-model-context.md) — Navigator model context interface
- [Execute callback](../02-specification/execute-callback.md) — Execute callback
- [Chrome Labs toolkit](../08-ecosystem/official-tools/chrome-labs-toolkit.md) — Chrome Labs toolkit
- [Model Context Tool Inspector](../08-ecosystem/official-tools/model-context-tool-inspector.md) — Model Context Tool Inspector
- [Chrome setup guide](chrome-setup.md) — Chrome setup guide
- [Imperative API deep dive](imperative-api-tutorial.md) — Imperative API deep dive
- [Declarative API deep dive](declarative-api-tutorial.md) — Declarative API deep dive
- [Security checklist](../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../15-reference/api-reference.md) — Complete API reference
- [WebMCP glossary](../01-overview/glossary.md) — WebMCP glossary
