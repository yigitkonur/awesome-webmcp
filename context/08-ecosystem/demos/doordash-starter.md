# Doriandarko/webmcp-starter — "Midnight Eats"

> Single-file "Midnight Eats" DoorDash-style demo with 9 WebMCP tools (8 imperative, 1 declarative), zero dependencies.

## Overview

[Doriandarko/webmcp-starter](https://github.com/Doriandarko/webmcp-starter) is a single-file WebMCP demo that implements a DoorDash-style food ordering interface called "Midnight Eats." It demonstrates both imperative and declarative WebMCP APIs with 9 tools and zero external dependencies — everything runs in a single HTML file.

- **Repository**: [github.com/Doriandarko/webmcp-starter](https://github.com/Doriandarko/webmcp-starter)
- **Author**: Doriandarko ([@Doriandarko](https://github.com/Doriandarko))
- **License**: MIT
- **Dependencies**: None (single HTML file)

## Why It Matters

This demo is the **simplest possible** WebMCP implementation. It proves that:
1. WebMCP tools can be added to any site with zero build tools
2. Both API styles (imperative + declarative) can coexist
3. A functional AI-operable application fits in a single file
4. No frameworks, no npm packages, no bundlers required

## The 9 Tools

### Imperative Tools (8)

#### 1. `search_restaurants`
```javascript
{
  name: "search_restaurants",
  description: "Search for restaurants by cuisine or name",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search term" },
      cuisine: { type: "string", description: "Cuisine filter" }
    },
    required: ["query"]
  },
  annotations: { readOnlyHint: true }
}
```

#### 2. `get_menu`
```javascript
{
  name: "get_menu",
  description: "Get the menu for a restaurant",
  inputSchema: {
    type: "object",
    properties: {
      restaurantId: { type: "string" }
    },
    required: ["restaurantId"]
  },
  annotations: { readOnlyHint: true }
}
```

#### 3. `add_to_cart`
```javascript
{
  name: "add_to_cart",
  description: "Add an item to the cart",
  inputSchema: {
    type: "object",
    properties: {
      itemId: { type: "string" },
      quantity: { type: "number", minimum: 1 },
      specialInstructions: { type: "string" }
    },
    required: ["itemId"]
  }
}
```

#### 4. `remove_from_cart`
```javascript
{
  name: "remove_from_cart",
  description: "Remove an item from the cart",
  inputSchema: {
    type: "object",
    properties: {
      itemId: { type: "string" }
    },
    required: ["itemId"]
  }
}
```

#### 5. `get_cart`
```javascript
{
  name: "get_cart",
  description: "Get current cart contents and total",
  inputSchema: { type: "object", properties: {} },
  annotations: { readOnlyHint: true }
}
```

#### 6. `clear_cart`
```javascript
{
  name: "clear_cart",
  description: "Empty the cart",
  inputSchema: { type: "object", properties: {} }
}
```

#### 7. `apply_promo_code`
```javascript
{
  name: "apply_promo_code",
  description: "Apply a promotional code to the order",
  inputSchema: {
    type: "object",
    properties: {
      code: { type: "string", description: "Promo code (e.g. WELCOME20, FREEDELIVERY, SAVE5)" }
    },
    required: ["code"]
  }
}
```

#### 8. `get_order_status`
```javascript
{
  name: "get_order_status",
  description: "Track the status of a placed order",
  inputSchema: {
    type: "object",
    properties: {
      orderId: { type: "string" }
    },
    required: ["orderId"]
  },
  annotations: { readOnlyHint: true }
}
```

### Declarative Tool (1)

#### 9. Checkout Form
```html
<form toolname="checkout"
      tooldescription="Complete the order checkout"
      action="/api/checkout" method="post">
  <input type="text" name="address" required
         toolparamdescription="Delivery address">
  <input type="tel" name="phone" required
         toolparamdescription="Contact phone number">
  <textarea name="instructions"
            toolparamdescription="Special delivery instructions">
  </textarea>
  <button type="submit">Place Order</button>
</form>
```

## Full Source Structure

The entire application is a single `food-app.html` file:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Midnight Eats</title>
  <style>
    /* Complete CSS styling (~200 lines) */
  </style>
</head>
<body>
  <!-- Restaurant listing UI -->
  <!-- Cart sidebar -->
  <!-- Checkout form (declarative tool) -->

  <script>
    // Mock data (restaurants, menus, etc.)
    // 8 imperative tool registrations
    // UI interaction logic
    // ~400 lines of JavaScript
  </script>
</body>
</html>
```

## Running the Demo

```bash
# Clone the repository
git clone https://github.com/Doriandarko/webmcp-starter.git
cd webmcp-starter

# Open directly in Chrome (no build step needed)
open food-app.html

# Or serve with any static server
npx serve .
python3 -m http.server 8080
```

## AI Interaction Example

```
User: "I want to order sushi for delivery to 123 Main St"

AI Agent:
  → search_restaurants({ query: "sushi" })
  → Found: "Tokyo Nights" (id: "rest-1"), "Sushi Master" (id: "rest-2")

  → get_menu({ restaurantId: "rest-1" })
  → Menu items: Salmon Roll ($12), Dragon Roll ($15), Miso Soup ($5)

  → add_to_cart({ itemId: "salmon-roll", quantity: 2 })
  → add_to_cart({ itemId: "miso-soup", quantity: 1 })

  → get_cart()
  → Total: $29.00

  → checkout (declarative form with address: "123 Main St")
  → Order placed!
```

## Key Teaching Points

1. **Zero dependencies**: Proves WebMCP needs no framework
2. **Mixed API**: Shows imperative + declarative coexisting
3. **Proper annotations**: Uses `readOnlyHint` for search/get tools
4. **User interaction**: Checkout requires human confirmation
5. **Complete app**: Not just a demo, but a functional food ordering flow

## See Also

- [Travel Demo](./travel-demo.md) — Official Chrome team demo
- [Kanban Demo](./kanban-demo.md) — React kanban with 8 tools
- [Calendar Demo](./calendar-demo.md) — Next.js calendar with 15 tools
- [Playground (webmcp.sh)](./playground-webmcp-sh.md) — Interactive playground
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Declarative API (PR #76)](https://github.com/webmachinelearning/webmcp/pull/76) — HTML form tools
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Input schema specification](../../02-specification/input-schema.md) — Input schema specification
- [E-commerce use case](../../11-use-cases/e-commerce.md) — E-commerce use case
- [Form filling use case](../../11-use-cases/form-filling.md) — Form filling use case
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
