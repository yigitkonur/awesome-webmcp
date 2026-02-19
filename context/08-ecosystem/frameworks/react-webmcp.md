# @mcp-b/react-webmcp

> React hooks (`useWebMCP`, `useMcpTool`) for registering and invoking WebMCP tools in React applications.

## Overview

[@mcp-b/react-webmcp](https://www.npmjs.com/package/@mcp-b/react-webmcp) is a React library providing hooks for seamless integration with the WebMCP API. It allows React developers to register tools via `navigator.modelContext` using familiar hook patterns and invoke tools programmatically from within components.

- **npm**: [@mcp-b/react-webmcp](https://www.npmjs.com/package/@mcp-b/react-webmcp)
- **Source**: Part of the [MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP) monorepo
- **License**: AGPL-3.0
- **Framework**: React 18+

## Key Hooks

### `useWebMCP()`

Registers WebMCP tools that persist across component lifecycle:

```jsx
import { useWebMCP } from '@mcp-b/react-webmcp';

function FlightSearch() {
  const [results, setResults] = useState([]);

  useWebMCP({
    tools: [
      {
        name: "searchFlights",
        description: "Search for available flights",
        inputSchema: {
          type: "object",
          properties: {
            origin: { type: "string", description: "IATA code" },
            destination: { type: "string", description: "IATA code" },
            date: { type: "string", format: "date" }
          },
          required: ["origin", "destination", "date"]
        },
        execute: async ({ origin, destination, date }) => {
          const flights = await searchFlightsAPI(origin, destination, date);
          setResults(flights);
          return {
            content: [{ type: "text", text: JSON.stringify(flights) }]
          };
        }
      }
    ]
  });

  return (
    <div>
      <h1>Flight Search</h1>
      {results.map(flight => (
        <FlightCard key={flight.id} flight={flight} />
      ))}
    </div>
  );
}
```

#### Features

- **Auto-registration**: Tools are registered when the component mounts
- **Auto-cleanup**: Tools are unregistered when the component unmounts
- **Dependency tracking**: Re-registers tools when dependencies change
- **Multiple tools**: Register multiple tools in a single hook call
- **Stable references**: Memoizes tool definitions to prevent unnecessary re-registrations

### `useMcpTool()`

Invokes a registered WebMCP tool and manages its execution state:

```jsx
import { useMcpTool } from '@mcp-b/react-webmcp';

function FlightBooker() {
  const { execute, loading, result, error } = useMcpTool("bookFlight");

  const handleBook = async () => {
    await execute({
      flightId: "BA005",
      passengers: 2,
      seatClass: "economy"
    });
  };

  return (
    <div>
      <button onClick={handleBook} disabled={loading}>
        {loading ? "Booking..." : "Book Flight"}
      </button>
      {result && <p>Booking confirmed: {result}</p>}
      {error && <p className="error">{error.message}</p>}
    </div>
  );
}
```

#### Returns

| Property | Type | Description |
|----------|------|-------------|
| `execute` | `(params) => Promise<any>` | Function to invoke the tool |
| `loading` | `boolean` | Whether the tool is currently executing |
| `result` | `any` | Last successful result |
| `error` | `Error \| null` | Last error, if any |

## Installation

```bash
# npm
npm install @mcp-b/react-webmcp @mcp-b/global

# pnpm
pnpm add @mcp-b/react-webmcp @mcp-b/global

# yarn
yarn add @mcp-b/react-webmcp @mcp-b/global
```

## Setup

### With Polyfill

Ensure the WebMCP polyfill is loaded before using hooks:

```jsx
// App entry point (index.js or main.jsx)
import '@mcp-b/global'; // Install navigator.modelContext polyfill
import { createRoot } from 'react-dom/client';
import App from './App';

createRoot(document.getElementById('root')).render(<App />);
```

### With Native WebMCP (Chrome 146+)

No additional setup needed — the hooks work directly with native `navigator.modelContext`:

```jsx
import { useWebMCP } from '@mcp-b/react-webmcp';

function App() {
  useWebMCP({
    tools: [/* ... */]
  });
  return <div>My WebMCP App</div>;
}
```

## Advanced Usage

### Dynamic Tool Registration

```jsx
function DynamicTools({ features }) {
  const tools = useMemo(() => {
    const toolList = [];
    if (features.includes("search")) {
      toolList.push({
        name: "search",
        description: "Search the catalog",
        inputSchema: { /* ... */ },
        execute: async (params) => { /* ... */ }
      });
    }
    if (features.includes("cart")) {
      toolList.push({
        name: "addToCart",
        description: "Add item to shopping cart",
        inputSchema: { /* ... */ },
        execute: async (params) => { /* ... */ }
      });
    }
    return toolList;
  }, [features]);

  useWebMCP({ tools });

  return <div>Features: {features.join(", ")}</div>;
}
```

### Tool Annotations

```jsx
useWebMCP({
  tools: [
    {
      name: "deleteAccount",
      description: "Permanently delete user account",
      inputSchema: { /* ... */ },
      annotations: {
        destructiveHint: true,
        readOnlyHint: false,
        idempotentHint: false
      },
      execute: async (params, agent) => {
        const confirmed = await agent.requestUserInteraction(async () => {
          return window.confirm("Are you sure you want to delete your account?");
        });
        if (!confirmed) throw new Error("Cancelled by user");
        // proceed with deletion
      }
    }
  ]
});
```

### Multiple Components Registering Tools

```jsx
function SearchSection() {
  useWebMCP({
    tools: [{ name: "search", /* ... */ }]
  });
  return <SearchUI />;
}

function CartSection() {
  useWebMCP({
    tools: [{ name: "addToCart", /* ... */ }, { name: "removeFromCart", /* ... */ }]
  });
  return <CartUI />;
}

function App() {
  return (
    <>
      <SearchSection />
      <CartSection />
      {/* All tools from all sections are available to AI agents */}
    </>
  );
}
```

## TypeScript Support

The hooks are fully typed:

```typescript
import { useWebMCP, useMcpTool } from '@mcp-b/react-webmcp';

interface SearchParams {
  query: string;
  category?: string;
}

interface SearchResult {
  id: string;
  name: string;
  price: number;
}

// Type-safe tool registration
useWebMCP({
  tools: [
    {
      name: "search",
      description: "Search products",
      inputSchema: { /* JSON Schema */ },
      execute: async (params: SearchParams): Promise<{ content: any[] }> => {
        // params are typed
      }
    }
  ]
});

// Type-safe tool invocation
const { execute } = useMcpTool<SearchParams, SearchResult>("search");
```

## Requirements

- React 18+
- @mcp-b/global (polyfill) or Chrome 146+ (native WebMCP)
- TypeScript 5+ (optional, for type safety)

## See Also

- [@mcp-b/global Polyfill](../polyfills/mcp-b-global.md) — WebMCP polyfill required for non-Chrome browsers
- [MiguelsPizza/WebMCP](../polyfills/miguels-pizza-webmcp.md) — Parent MCP-B project
- [webmcp-kit](./webmcp-kit.md) — Alternative framework for adding WebMCP tools
- [Kanban Demo](../demos/kanban-demo.md) — React kanban board using WebMCP
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Form mapping for declarative tools](../../03-declarative-api/form-mapping.md) — Form mapping for declarative tools
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [Best practices](../../12-tutorials/best-practices.md) — Best practices
- [Form filling use case](../../11-use-cases/form-filling.md) — Form filling use case
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
