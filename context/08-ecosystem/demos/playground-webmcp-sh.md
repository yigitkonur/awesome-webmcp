# Playground — webmcp.sh

> Interactive browser playground for WebMCP, built with React 19, Vite, PGlite (in-browser Postgres), and Cloudflare Workers.

## Overview

[webmcp.sh](https://webmcp.sh/) is an interactive browser playground for connecting to MCP servers, running tools, and querying an in-browser PostgreSQL database. It serves as both a learning tool and a testing environment for WebMCP integrations.

- **Live Demo**: [webmcp.sh](https://webmcp.sh/)
- **Source Code**: [github.com/WebMCP-org/webmcp-sh](https://github.com/WebMCP-org/webmcp-sh)
- **Maintained by**: WebMCP-org
- **License**: MIT

## Key Features

### MCP Server Connection

Connect to any MCP server directly from the browser:

- **WebSocket transport**: Connect to local or remote MCP servers via WebSocket
- **SSE transport**: Server-Sent Events for streaming connections
- **Stdio emulation**: Bridge to stdio-based MCP servers via proxy

### Tool Execution

Discover and execute tools from connected MCP servers:

- **Tool browser**: Visual list of all available tools
- **Parameter editor**: Auto-generated forms from JSON Schema
- **Result viewer**: Formatted display of tool execution results
- **History**: Track recent tool invocations

### In-Browser PostgreSQL (PGlite)

Full PostgreSQL database running entirely in the browser via [PGlite](https://pglite.dev/):

```sql
-- Create tables, run queries, test data operations
CREATE TABLE flights (
  id SERIAL PRIMARY KEY,
  origin TEXT NOT NULL,
  destination TEXT NOT NULL,
  departure TIMESTAMP,
  price DECIMAL(10,2)
);

INSERT INTO flights (origin, destination, departure, price)
VALUES ('LHR', 'JFK', '2026-03-20 10:30', 1240.00);

SELECT * FROM flights WHERE price < 1000;
```

- **No server required**: Database runs in WebAssembly
- **Persistent storage**: Data persists in browser storage
- **Full SQL support**: Supports most PostgreSQL features
- **WebMCP integration**: Query results can be exposed as tools

### Code Editor

Built-in code editor for writing and testing WebMCP tool definitions:

```javascript
// Write and test tool definitions in the playground
navigator.modelContext.registerTool({
  name: "queryDatabase",
  description: "Run a SQL query on the in-browser database",
  inputSchema: {
    type: "object",
    properties: {
      sql: {
        type: "string",
        description: "SQL query to execute"
      }
    },
    required: ["sql"]
  },
  execute: async ({ sql }) => {
    const result = await pglite.query(sql);
    return {
      content: [{
        type: "text",
        text: JSON.stringify(result.rows, null, 2)
      }]
    };
  }
});
```

## Tech Stack

| Component | Technology |
|-----------|-----------|
| **Frontend** | React 19 |
| **Build Tool** | Vite |
| **Database** | PGlite (in-browser PostgreSQL via WASM) |
| **Hosting** | Cloudflare Workers |
| **Styling** | Tailwind CSS |
| **Code Editor** | Monaco Editor |
| **MCP Client** | Custom MCP client implementation |

## Architecture

```
┌──────────────────────────────────────────┐
│  Browser (webmcp.sh)                     │
│                                          │
│  ┌──────────────┐  ┌──────────────────┐  │
│  │  React 19 UI │  │  Monaco Editor   │  │
│  │  - Tool list │  │  - Code editing  │  │
│  │  - Results   │  │  - Tool defs     │  │
│  └──────┬───────┘  └──────┬───────────┘  │
│         │                 │              │
│  ┌──────▼─────────────────▼───────────┐  │
│  │  MCP Client                        │  │
│  │  - WebSocket / SSE connections     │  │
│  │  - Tool discovery & invocation     │  │
│  └──────┬─────────────────────────────┘  │
│         │                                │
│  ┌──────▼─────────────────────────────┐  │
│  │  PGlite (In-Browser PostgreSQL)    │  │
│  │  - WASM-based Postgres             │  │
│  │  - Full SQL support                │  │
│  │  - Persistent storage              │  │
│  └────────────────────────────────────┘  │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │  navigator.modelContext (Polyfill) │  │
│  │  - Tool registration              │  │
│  │  - WebMCP API surface              │  │
│  └────────────────────────────────────┘  │
└──────────────────────────────────────────┘
           │
     Cloudflare Workers (Hosting + Proxy)
```

## Usage

### Online

Visit [webmcp.sh](https://webmcp.sh/) — no installation required.

### Self-Hosted

```bash
git clone https://github.com/WebMCP-org/webmcp-sh.git
cd webmcp-sh
npm install
npm run dev
# Open http://localhost:5173
```

### Deploy to Cloudflare

```bash
npm run build
npx wrangler deploy
```

## Use Cases

| Use Case | Description |
|----------|-------------|
| **Learning** | Experiment with WebMCP API without setting up a project |
| **Testing** | Test MCP server connections and tool invocations |
| **Prototyping** | Prototype tool definitions before adding to production |
| **Debugging** | Debug tool schemas, parameters, and responses |
| **Database tools** | Create tools that query PGlite for data-driven demos |

## See Also

- [Travel Demo](./travel-demo.md) — Official Chrome team flight search demo
- [Kanban Demo](./kanban-demo.md) — React kanban board with WebMCP
- [DoorDash Starter](./doordash-starter.md) — Single-file zero-dependency demo
- [Chrome Labs Toolkit](../official-tools/chrome-labs-toolkit.md) — Official tools
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Navigator model context interface](../../02-specification/navigator-model-context.md) — Navigator model context interface
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Testing tools and workflows](../../12-tutorials/testing-tools.md) — Testing tools and workflows
- [Chrome setup guide](../../12-tutorials/chrome-setup.md) — Chrome setup guide
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
