# inputSchema — JSON Schema for Tool Inputs

## Overview

The `inputSchema` property of `ModelContextTool` defines the expected input parameters
for a tool using [JSON Schema Draft 2020-12](https://json-schema.org/draft/2020-12/json-schema-core.html).
It tells agents what arguments to provide when calling the tool (see [spec](https://webmachinelearning.github.io/webmcp)), including types,
descriptions, and validation constraints.

## Normative Reference

From the Bikeshed spec bibliography:

```json
{
  "json-schema": {
    "href": "https://json-schema.org/draft/2020-12/json-schema-core.html",
    "title": "JSON Schema: A Media Type for Describing JSON Documents",
    "publisher": "JSON Schema"
  }
}
```

From the [spec](https://webmachinelearning.github.io/webmcp):

> A JSON Schema object describing the expected input parameters for the tool.

## WebIDL

From [PR #75](https://github.com/webmachinelearning/webmcp/pull/75):

```webidl
dictionary ModelContextTool {
  // ...
  object inputSchema;
  // ...
};
```

The `inputSchema` is typed as `object` — a generic JavaScript object that must conform
to JSON Schema structure. The browser validates this on registration; if it's not a
valid JSON Schema, `registerTool()` throws a `TypeError`.

## Basic Structure

The `inputSchema` should be a JSON Schema of `type: "object"` with `properties` and
optional `required` array:

```javascript
{
  inputSchema: {
    type: "object",
    properties: {
      paramName: {
        type: "string",
        description: "What this parameter is for"
      }
    },
    required: ["paramName"]
  }
}
```

## Supported JSON Schema Types

| Type | JavaScript Equivalent | Example Values |
|---|---|---|
| `"string"` | `string` | `"hello"`, `"user@email.com"` |
| `"number"` | `number` | `42`, `3.14`, `-1` |
| `"integer"` | `number` (integer) | `42`, `0`, `-1` |
| `"boolean"` | `boolean` | `true`, `false` |
| `"array"` | `Array` | `[1, 2, 3]` |
| `"object"` | `Object` | `{ key: "value" }` |
| `"null"` | `null` | `null` |

## Schema Keywords

### type

Specifies the data type:

```javascript
inputSchema: {
  type: "object",
  properties: {
    name: { type: "string" },
    age: { type: "integer" },
    score: { type: "number" },
    active: { type: "boolean" }
  }
}
```

### description

Human-readable description of each parameter. This is **critical** for agents — it's how
they understand what value to provide:

```javascript
inputSchema: {
  type: "object",
  properties: {
    query: {
      type: "string",
      description: "The search query string. Supports boolean operators AND, OR, NOT."
    },
    maxResults: {
      type: "integer",
      description: "Maximum number of results to return. Defaults to 10 if not specified."
    }
  }
}
```

> **Best Practice:** Always include `description` for every property. Agents rely heavily
> on descriptions to determine correct values.

### required

Array of property names that must be provided:

```javascript
inputSchema: {
  type: "object",
  properties: {
    name: { type: "string", description: "Required: stamp name" },
    year: { type: "number", description: "Required: issue year" },
    imageUrl: { type: "string", description: "Optional: image URL" }
  },
  required: ["name", "year"]  // imageUrl is optional
}
```

### enum

Constrains a value to a fixed set of options:

```javascript
inputSchema: {
  type: "object",
  properties: {
    priority: {
      type: "string",
      enum: ["low", "medium", "high", "critical"],
      description: "Task priority level"
    },
    status: {
      type: "string",
      enum: ["open", "in-progress", "done"],
      description: "Current task status"
    }
  }
}
```

### Numeric Constraints

```javascript
inputSchema: {
  type: "object",
  properties: {
    quantity: {
      type: "integer",
      minimum: 1,
      maximum: 100,
      description: "Number of items (1-100)"
    },
    price: {
      type: "number",
      minimum: 0,
      exclusiveMinimum: 0,
      description: "Price in USD (must be positive)"
    },
    discount: {
      type: "number",
      minimum: 0,
      maximum: 1,
      description: "Discount percentage as decimal (0.0 to 1.0)"
    }
  }
}
```

### String Constraints

```javascript
inputSchema: {
  type: "object",
  properties: {
    email: {
      type: "string",
      format: "email",
      description: "User's email address"
    },
    date: {
      type: "string",
      format: "date",
      description: "Date in ISO 8601 format (YYYY-MM-DD)"
    },
    zipCode: {
      type: "string",
      pattern: "^[0-9]{5}(-[0-9]{4})?$",
      description: "US zip code (5 or 9 digit)"
    },
    username: {
      type: "string",
      minLength: 3,
      maxLength: 30,
      description: "Username (3-30 characters)"
    }
  }
}
```

### Array Types

```javascript
inputSchema: {
  type: "object",
  properties: {
    tags: {
      type: "array",
      items: { type: "string" },
      description: "List of tags to apply",
      minItems: 1,
      maxItems: 10
    },
    coordinates: {
      type: "array",
      items: { type: "number" },
      minItems: 2,
      maxItems: 2,
      description: "Latitude and longitude pair [lat, lng]"
    }
  }
}
```

### Nested Objects

```javascript
inputSchema: {
  type: "object",
  properties: {
    address: {
      type: "object",
      properties: {
        street: { type: "string", description: "Street address" },
        city: { type: "string", description: "City name" },
        state: { type: "string", description: "State/province code" },
        zip: { type: "string", description: "Postal code" }
      },
      required: ["street", "city"],
      description: "Shipping address"
    }
  },
  required: ["address"]
}
```

## Best Practices for Writing Good Schemas

### 1. Always Include Descriptions

Descriptions are the primary way agents understand parameters. Without them, agents
must guess from the property name alone.

```javascript
// ❌ Bad — no descriptions
inputSchema: {
  type: "object",
  properties: {
    q: { type: "string" },
    n: { type: "integer" }
  }
}

// ✅ Good — clear descriptions
inputSchema: {
  type: "object",
  properties: {
    query: {
      type: "string",
      description: "Search query. Supports partial matching and wildcards."
    },
    maxResults: {
      type: "integer",
      description: "Maximum number of results to return. Default is 10.",
      minimum: 1,
      maximum: 100
    }
  }
}
```

### 2. Use Specific Types

```javascript
// ❌ Bad — string for everything
{ year: { type: "string", description: "Year" } }

// ✅ Good — proper types with constraints
{ year: { type: "integer", minimum: 1800, maximum: 2100, description: "Issue year" } }
```

### 3. Minimize Required Parameters

Only mark parameters as required when they are truly necessary. Optional parameters
with good defaults give agents more flexibility:

```javascript
inputSchema: {
  type: "object",
  properties: {
    query: { type: "string", description: "Search query" },
    category: { type: "string", description: "Filter by category. Optional." },
    sortBy: { type: "string", enum: ["relevance", "price", "date"], description: "Sort order. Defaults to relevance." }
  },
  required: ["query"]  // Only query is truly required
}
```

### 4. Avoid Over-Parameterization

> **Security Warning:** Over-parameterized tools can extract sensitive user data.
> Agents may fill in optional parameters using personalization context.
> See [Issue #44](https://github.com/webmachinelearning/webmcp/issues/44) and the
> security considerations document.

```javascript
// ❌ Bad — asks for too much personal data
inputSchema: {
  type: "object",
  properties: {
    query: { type: "string" },
    age: { type: "number", description: "User's age for age-appropriate results" },
    location: { type: "string", description: "User's location" },
    income: { type: "string", description: "Income bracket" }
  }
}

// ✅ Good — only what's needed
inputSchema: {
  type: "object",
  properties: {
    query: { type: "string", description: "Search query" }
  },
  required: ["query"]
}
```

### 5. Use enum for Known Value Sets

```javascript
// ❌ Bad — no guidance on valid values
{ size: { type: "string", description: "Clothing size" } }

// ✅ Good — explicit valid values
{ size: { type: "string", enum: ["XS", "S", "M", "L", "XL", "XXL"], description: "Clothing size" } }
```

## Complete Example: E-commerce Tool

```javascript
navigator.modelContext.registerTool({
  name: "search-products",
  description: "Search the product catalog. Returns matching products with prices and availability.",
  inputSchema: {
    type: "object",
    properties: {
      query: {
        type: "string",
        description: "Search query. Matches against product names and descriptions.",
        minLength: 1,
        maxLength: 200
      },
      category: {
        type: "string",
        enum: ["electronics", "clothing", "home", "books", "sports"],
        description: "Filter by product category. Omit to search all categories."
      },
      priceRange: {
        type: "object",
        properties: {
          min: { type: "number", minimum: 0, description: "Minimum price in USD" },
          max: { type: "number", minimum: 0, description: "Maximum price in USD" }
        },
        description: "Optional price range filter"
      },
      sortBy: {
        type: "string",
        enum: ["relevance", "price-low", "price-high", "rating", "newest"],
        description: "Sort order for results. Defaults to relevance."
      },
      limit: {
        type: "integer",
        minimum: 1,
        maximum: 50,
        description: "Maximum number of results. Defaults to 10."
      }
    },
    required: ["query"]
  },
  execute: async ({ query, category, priceRange, sortBy, limit }) => {
    const results = await searchCatalog({ query, category, priceRange, sortBy, limit });
    return {
      content: [{
        type: "text",
        text: JSON.stringify(results)
      }]
    };
  }
});
```

## Tools Without inputSchema

When a tool has no parameters, omit `inputSchema` entirely:

```javascript
navigator.modelContext.registerTool({
  name: "get-cart-count",
  description: "Get the number of items in the shopping cart",
  execute: async () => ({
    content: [{ type: "text", text: String(cart.items.length) }]
  })
});
```

## Validation on Registration

The browser validates `inputSchema` when `registerTool()` or `provideContext()` is called
(see [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md)).
Invalid schemas cause a `TypeError`:

```javascript
try {
  navigator.modelContext.registerTool({
    name: "broken",
    description: "This will fail",
    inputSchema: "not-an-object",  // Invalid!
    execute: async () => {}
  });
} catch (e) {
  console.error(e); // TypeError
}
```

## Related Issues

- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Output schema discussion
- [Issue #11 — Prompt injection](https://github.com/webmachinelearning/webmcp/issues/11) — Security risks in descriptions

## See Also

- [outputSchema for structured outputs](output-schema.md)
- [Tool registration interface](tool-registration-interface.md)
- [registerTool() method](register-tool.md)
- [Execute callback specification](execute-callback.md)
- [HTML form to JSON Schema mapping](../03-declarative-api/form-mapping.md)
- [toolparamdescription attribute](../03-declarative-api/toolparamdescription-attribute.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Privacy leakage through over-parameterization](../04-security/privacy-leakage.md)
- [API reference](../15-reference/api-reference.md)
