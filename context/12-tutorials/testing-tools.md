# Testing WebMCP Tools

> How to test and debug WebMCP tools — using the Model Context Tool Inspector, manual testing patterns, integration testing with agents, and the WebMCP Evals CLI.

---

## Overview

Testing WebMCP tools requires verifying both the tool schema (does the agent understand what the tool does?) and the tool execution (does the tool actually work correctly?). This guide covers the full testing spectrum from manual inspection to automated evaluation.

---

## Model Context Tool Inspector Extension

The primary testing tool for WebMCP development.

### Installation

Install from the [Chrome Web Store](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd).

Source code: [beaufortfrancois/model-context-tool-inspector](https://github.com/beaufortfrancois/model-context-tool-inspector)

### Features

| Feature | Description |
|---------|-------------|
| **Tool Discovery** | Lists all imperative and declarative tools on the page |
| **Schema Inspection** | View the complete inputSchema for each tool |
| **Manual Execution** | Input parameters and invoke tools directly |
| **Response Display** | View the content array returned by tools |
| **Error Display** | Show error messages from failed tool calls |
| **Gemini Integration** | Test via natural language using Gemini API |

### Usage Workflow

1. **Navigate** to your page with WebMCP tools
2. **Open** the inspector by clicking the extension icon
3. **Verify** all expected tools are listed
4. **Expand** a tool to see its schema
5. **Fill** in test parameters
6. **Execute** and check the response
7. **Check** that the page UI updated correctly

### Example Testing Session

```
Inspector shows:
  📦 search-products (imperative)
    - query: string (required)
    - category: string [enum]
    - max_price: number

  📋 contact-form (declarative, toolautosubmit)
    - name: string (required)
    - email: string (required)
    - message: string

Test 1: search-products
  Input: { query: "laptop", category: "electronics", max_price: 1000 }
  Expected: Page shows filtered products, response contains JSON array
  ✅ Result: 15 products returned, UI updated

Test 2: search-products (error case)
  Input: { query: "" }  (missing required field)
  Expected: Validation error
  ✅ Result: Error "query is required"

Test 3: contact-form
  Input: { name: "Test", email: "test@example.com", message: "Hello" }
  Expected: Form fields pre-filled, ready for submission
  ✅ Result: Form filled, :tool-form-active style applied
```

---

## Manual Testing Patterns

### Pattern 1: DevTools Console Testing

Test tools directly from the browser console:

```js
// Verify tool registration
console.log("modelContext" in navigator); // true

// Test feature detection pattern
if ("modelContext" in navigator) {
  console.log("WebMCP available - tools should be registered");
}
```

### Pattern 2: Happy Path Testing

Test each tool with valid, expected inputs:

```js
// Create a checklist of tools and test cases
const testCases = [
  { tool: "search-products", input: { query: "shoes" }, expect: "returns products" },
  { tool: "add-to-cart", input: { product_id: "123", quantity: 1 }, expect: "item added" },
  { tool: "view-cart", input: {}, expect: "shows cart contents" }
];
```

### Pattern 3: Edge Case Testing

Test boundary conditions and error handling:

```
Test: Empty string parameters
  Input: { query: "" }
  Expected: Descriptive error message

Test: Extreme values
  Input: { quantity: 99999 }
  Expected: Error about maximum quantity

Test: Invalid enum value
  Input: { category: "nonexistent" }
  Expected: Handled gracefully

Test: Missing required parameters
  Input: {}
  Expected: Clear error about required fields

Test: Extra unexpected parameters
  Input: { query: "test", extra_field: "ignored" }
  Expected: Extra field ignored, tool works normally
```

### Pattern 4: State-Dependent Testing

For tools that change based on application state:

```
Scenario: User not logged in
  Expected tools: [search, view-product]
  NOT expected: [add-to-cart, checkout]

Scenario: User logged in
  Expected tools: [search, view-product, add-to-cart, checkout]

Scenario: Cart is empty
  Tool: checkout → Expected: Error "Cart is empty"

Scenario: Cart has items
  Tool: checkout → Expected: Payment flow initiated
```

---

## WebMCP Evals CLI

For automated testing of WebMCP tools at scale, the community has developed evaluation tools.

### Concept

An evaluation CLI can:
1. Load a page in a headless browser
2. Discover registered tools
3. Execute tools with predefined test cases
4. Validate responses against expected outcomes
5. Generate test reports

### Example Evaluation Script

```js
// webmcp-eval.js — conceptual test runner
const testSuite = {
  url: "http://localhost:8080",
  tests: [
    {
      name: "search returns results",
      tool: "search-products",
      input: { query: "laptop" },
      assertions: [
        { type: "response_contains", value: "items" },
        { type: "response_length_gt", value: 0 },
        { type: "ui_updated", selector: "#product-grid" }
      ]
    },
    {
      name: "invalid product ID returns error",
      tool: "add-to-cart",
      input: { product_id: "nonexistent" },
      assertions: [
        { type: "throws_error", message_contains: "not found" }
      ]
    },
    {
      name: "declarative form detected",
      tool: "contact-form",
      assertions: [
        { type: "tool_exists" },
        { type: "has_param", name: "email", required: true }
      ]
    }
  ]
};
```

---

## Integration Testing with Agents

### Testing with Gemini via Inspector

1. Open the Model Context Tool Inspector
2. Click Settings → Enter Gemini API key
3. Type natural language queries:
   - "Search for red shoes under $50"
   - "Add the first result to my cart"
   - "What's in my cart?"
4. Verify the agent selects the correct tool and parameters

### Testing Workflow Sequences

Test multi-step agent workflows:

```
Step 1: "Show me laptop options"
  → Agent calls search-products({ query: "laptop" })
  ✓ Verify: Products displayed

Step 2: "Add the cheapest one to my cart"
  → Agent calls add-to-cart({ product_id: "...", quantity: 1 })
  ✓ Verify: Cart updated, badge shows 1

Step 3: "What's my cart total?"
  → Agent calls view-cart()
  ✓ Verify: Returns correct total

Step 4: "Proceed to checkout"
  → Agent calls checkout()
  → requestUserInteraction fires
  ✓ Verify: Confirmation dialog appears
```

---

## Debugging Tips

### Common Issues and Solutions

| Issue | Debug Approach |
|-------|---------------|
| Tool not appearing in inspector | Check console for registration errors; verify feature detection |
| Tool executes but UI doesn't update | Add `console.log` in execute; check if render functions are called |
| Error not reaching agent | Ensure you `throw new Error()` (not just `return`) |
| Declarative form not detected | Verify both `toolname` AND `tooldescription` are present |
| Async tool hangs | Check if Promise is resolving; add timeout handling |
| Wrong parameters received | Log params at start of execute: `console.log("Params:", params)` |

### Debug Logging Pattern

Add structured logging to your tools during development:

```js
navigator.modelContext.registerTool({
  name: "my-tool",
  description: "...",
  inputSchema: { ... },
  execute(params, agent) {
    console.group("🔧 Tool: my-tool");
    console.log("Input params:", JSON.stringify(params, null, 2));

    try {
      // Your tool logic
      const result = doSomething(params);

      console.log("Result:", result);
      console.groupEnd();

      return {
        content: [{ type: "text", text: JSON.stringify(result) }]
      };
    } catch (error) {
      console.error("Error:", error.message);
      console.groupEnd();
      throw error;
    }
  }
});
```

### Breakpoint Debugging

1. Open DevTools → Sources tab
2. Find your JavaScript file
3. Set a breakpoint inside the `execute` function
4. Use the inspector extension to invoke the tool
5. Execution pauses at the breakpoint
6. Inspect variables, step through code, check state

---

## Testing Declarative Tools Specifically

### Verify Schema Generation

Check that the browser correctly generates schemas from your forms:

```js
// After page load, the inspector shows auto-generated schemas
// Verify:
// 1. All form fields appear as parameters
// 2. Required fields are marked required
// 3. Select options appear as enums
// 4. Input types map to schema types correctly
```

### Test Form Pre-filling

When invoking a declarative tool:
1. Verify each form field gets the correct value
2. Check that `:tool-form-active` CSS is applied
3. Confirm `toolactivated` event fires
4. Test cancellation → `toolcancel` event fires
5. Verify `agentInvoked` is `true` on submit event

### Test toolautosubmit Behavior

```
Without toolautosubmit:
  → Form fills but waits for user click
  ✓ User can review and modify values

With toolautosubmit:
  → Form fills and submits automatically
  ✓ Submission visible to user
  ✓ Form action/method used correctly
```

---

## Testing Checklist

```markdown
## Pre-launch Testing Checklist

### Tool Registration
- [ ] Feature detection (`"modelContext" in navigator`) works
- [ ] All tools appear in Model Context Tool Inspector
- [ ] Tool names are descriptive and unique
- [ ] Tool descriptions accurately describe behavior
- [ ] inputSchema correctly defines all parameters

### Execution
- [ ] Happy path works for all tools
- [ ] Error cases return descriptive messages
- [ ] UI updates after tool execution
- [ ] Async tools resolve correctly
- [ ] requestUserInteraction works for sensitive operations

### Declarative Forms
- [ ] Forms detected with toolname + tooldescription
- [ ] Form fields map to correct schema parameters
- [ ] toolautosubmit only on safe operations
- [ ] agentInvoked detection works in submit handler
- [ ] CSS pseudo-classes apply correctly

### Edge Cases
- [ ] Missing optional parameters handled
- [ ] Invalid parameter values rejected with clear errors
- [ ] Concurrent tool calls handled (or properly queued)
- [ ] Page navigation during tool execution handled
- [ ] Large responses don't crash the agent
```

---

## See Also

- [Getting Started](./getting-started.md) — Initial setup and first tool
- [Chrome Setup Guide](./chrome-setup.md) — Browser configuration
- [Imperative API Tutorial](./imperative-api-tutorial.md) — Building testable tools
- [Best Practices](./best-practices.md) — Error handling and validation patterns
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../02-specification/register-tool.md) — Tool registration API
- [WebMCP Evals CLI](../08-ecosystem/official-tools/webmcp-evals-cli.md) — WebMCP Evals CLI
- [Chrome Labs toolkit](../08-ecosystem/official-tools/chrome-labs-toolkit.md) — Chrome Labs toolkit
- [Model Context Tool Inspector](../08-ecosystem/official-tools/model-context-tool-inspector.md) — Model Context Tool Inspector
- [Developer tooling use case](../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Security checklist](../04-security/developer-security-checklist.md) — Security checklist
- [Error handling reference](../15-reference/error-handling.md) — Error handling reference
