# tuvit/webmcp — Wix WebMCP Extension

> Wix platform extension injecting WebMCP attributes into Wix Stores pages for AI agent access to e-commerce data.

## Overview

[tuvit/webmcp](https://github.com/tuvit/webmcp) is a Wix platform extension that adds WebMCP support to Wix-hosted e-commerce stores. It injects WebMCP declarative attributes into Wix Stores product pages, cart, and checkout flows, enabling AI agents to interact with Wix e-commerce functionality through the standardized `navigator.modelContext` API.

- **Repository**: [github.com/tuvit/webmcp](https://github.com/tuvit/webmcp)
- **Author**: [@tuvit](https://github.com/tuvit)
- **Platform**: Wix (Velo by Wix)
- **License**: MIT

## Key Features

### Wix Stores Integration

Automatically adds WebMCP attributes to Wix Stores pages:

#### Product Pages

```html
<!-- Wix product page with WebMCP attributes injected -->
<form toolname="add_to_cart"
      tooldescription="Add this product to the shopping cart"
      toolautosubmit="false">

  <select name="size"
          toolparamdescription="Product size (S, M, L, XL)">
    <option value="S">Small</option>
    <option value="M">Medium</option>
    <option value="L">Large</option>
    <option value="XL">Extra Large</option>
  </select>

  <select name="color"
          toolparamdescription="Product color variant">
    <option value="black">Black</option>
    <option value="white">White</option>
    <option value="blue">Blue</option>
  </select>

  <input type="number" name="quantity" min="1" max="10" value="1"
         toolparamdescription="Number of items to add">

  <button type="submit">Add to Cart</button>
</form>
```

#### Product Search

```javascript
// Imperative tool registration for product search
navigator.modelContext.registerTool({
  name: "searchProducts",
  description: "Search products in this Wix store",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search keyword" },
      category: { type: "string", description: "Product collection name" },
      minPrice: { type: "number", description: "Minimum price filter" },
      maxPrice: { type: "number", description: "Maximum price filter" },
      inStock: { type: "boolean", description: "Only show in-stock items" }
    },
    required: ["query"]
  },
  annotations: { readOnlyHint: true },
  execute: async (params) => {
    // Uses Wix Data API internally
    const results = await wixData.query("Stores/Products")
      .contains("name", params.query)
      .find();
    return { content: [{ type: "text", text: JSON.stringify(results.items) }] };
  }
});
```

#### Cart Management

```javascript
navigator.modelContext.registerTool({
  name: "getCart",
  description: "Get current shopping cart contents",
  inputSchema: { type: "object", properties: {} },
  annotations: { readOnlyHint: true },
  execute: async () => {
    const cart = await wixStoresCart.getCurrentCart();
    return { content: [{ type: "text", text: JSON.stringify(cart) }] };
  }
});

navigator.modelContext.registerTool({
  name: "updateCartItem",
  description: "Update quantity of an item in the cart",
  inputSchema: {
    type: "object",
    properties: {
      lineItemId: { type: "string", description: "Cart line item ID" },
      quantity: { type: "number", description: "New quantity" }
    },
    required: ["lineItemId", "quantity"]
  },
  annotations: { idempotentHint: true },
  execute: async ({ lineItemId, quantity }) => {
    await wixStoresCart.updateLineItemQuantity(lineItemId, quantity);
    return { content: [{ type: "text", text: "Cart updated" }] };
  }
});
```

### Available Tools

| Tool | Type | Description |
|------|------|-------------|
| `searchProducts` | Imperative | Search products by keyword, category, price |
| `getProductDetails` | Imperative | Get product details by ID or slug |
| `add_to_cart` | Declarative | Add product to cart (form-based) |
| `getCart` | Imperative | Get current cart contents |
| `updateCartItem` | Imperative | Update cart item quantity |
| `removeCartItem` | Imperative | Remove item from cart |
| `getCollections` | Imperative | List product collections/categories |
| `getStoreInfo` | Imperative | Get store name, currency, policies |

## Installation

### Via Wix App Market

1. Go to your Wix Dashboard
2. Navigate to **App Market**
3. Search for **"WebMCP"** by tuvit
4. Click **Add to Site**
5. Configure tool settings in the app dashboard

### Via Velo (Developer Setup)

```javascript
// In your Wix site's page code (Velo)
import { webmcpInit } from '@tuvit/webmcp';

$w.onReady(function () {
  webmcpInit({
    enableProductSearch: true,
    enableCartTools: true,
    enableCheckout: false, // Requires user interaction
    storeId: "your-store-id"
  });
});
```

## Configuration

### App Dashboard Settings

```
WebMCP Settings
├── Tools
│   ├── Product Search: ✅
│   ├── Product Details: ✅
│   ├── Add to Cart: ✅
│   ├── Cart Management: ✅
│   ├── Checkout: ❌ (requires user interaction)
│   └── Order History: ❌ (requires auth)
├── Declarative Attributes
│   ├── Product forms: ✅
│   ├── Contact forms: ✅
│   └── Custom forms: Configure...
└── Security
    ├── Rate limit: 30 req/min
    └── Require user interaction for purchases: ✅
```

## Architecture

```
┌──────────────────────────────────────┐
│  Wix Site                            │
│                                      │
│  ┌────────────────────────────────┐  │
│  │  Wix Stores                   │  │
│  │  (Products, Cart, Checkout)   │  │
│  └──────────┬────────────────────┘  │
│             │                       │
│  ┌──────────▼────────────────────┐  │
│  │  tuvit/webmcp Extension       │  │
│  │  - Attribute injector         │  │
│  │  - Tool registrar             │  │
│  │  - Wix Data API adapter       │  │
│  └──────────┬────────────────────┘  │
│             │                       │
│  ┌──────────▼────────────────────┐  │
│  │  navigator.modelContext       │  │
│  │  (Browser API)                │  │
│  └───────────────────────────────┘  │
└──────────────────────────────────────┘
```

## E-Commerce Use Cases

| Use Case | Tool(s) Used | User Interaction? |
|----------|-------------|-------------------|
| "Find blue sneakers under $100" | `searchProducts` | No |
| "Add this to my cart in size M" | `add_to_cart` | No |
| "What's in my cart?" | `getCart` | No |
| "Change quantity to 3" | `updateCartItem` | No |
| "Complete my purchase" | `checkout` | ✅ Yes (required) |

## Requirements

- Wix Premium plan (for Velo access)
- Wix Stores app installed
- Chrome 146+ (or polyfill) for AI agent access

## See Also

- [WordPress wmcp.dev](./wordpress-wmcp-dev.md) — WordPress WebMCP plugin
- [WordPress wp-ai-connect](./wordpress-wp-ai-connect.md) — WordPress REST API for WebMCP
- [Declarative API (PR #76)](https://github.com/webmachinelearning/webmcp/pull/76) — W3C declarative spec
- [User Interaction (Issue #21)](https://github.com/webmachinelearning/webmcp/issues/21) — requestUserInteraction for purchases
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Iframe registration](../../07-architecture/iframe-registration.md) — Iframe registration
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [E-commerce use case](../../11-use-cases/e-commerce.md) — E-commerce use case
- [SEO implications](../../11-use-cases/seo-implications.md) — SEO implications
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
