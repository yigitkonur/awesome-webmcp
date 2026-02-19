# grzetich/webmcp-kanban

> React kanban board with 8 AI-callable WebMCP tools, drag-and-drop, and localStorage persistence.

## Overview

[grzetich/webmcp-kanban](https://github.com/grzetich/webmcp-kanban) is a React-based kanban board application that registers 8 WebMCP tools, enabling AI agents to create, move, update, and delete cards through structured tool calls. It demonstrates how a project management UI can be fully AI-operable while maintaining a traditional drag-and-drop interface for human users.

- **Repository**: [github.com/grzetich/webmcp-kanban](https://github.com/grzetich/webmcp-kanban)
- **Live Demo**: [webmcp-kanban.vercel.app](https://webmcp-kanban.vercel.app)
- **Author**: [@grzetich](https://github.com/grzetich)
- **Framework**: React 18 + Vite + Tailwind CSS
- **License**: MIT
- **Polyfill**: Uses [@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global) and [@mcp-b/react-webmcp](https://www.npmjs.com/package/@mcp-b/react-webmcp) with Zod schemas

## WebMCP Tools (8 Total)

### 1. `get_board`

Get the entire board state (all columns and cards):

```javascript
{
  name: "get_board",
  description: "Returns all columns and cards with their positions, priorities, and labels",
  inputSchema: { type: "object", properties: {} },
  annotations: { readOnlyHint: true }
}
```

### 2. `create_card`

Create a new card in a specified column:

```javascript
{
  name: "create_card",
  description: "Creates a new card in a specified column",
  inputSchema: {
    type: "object",
    properties: {
      title: { type: "string", description: "Card title" },
      description: { type: "string", description: "Card description" },
      priority: { type: "string", description: "Card priority level" },
      labels: { type: "array", description: "Card labels" },
      column: { type: "string", description: "Target column" }
    },
    required: ["title"]
  }
}
```

### 3. `move_card`

Move a card to a different column:

```javascript
{
  name: "move_card",
  description: "Moves a card to a different column",
  inputSchema: {
    type: "object",
    properties: {
      cardId: { type: "string", description: "Card ID to move" },
      toColumn: { type: "string", description: "Destination column" }
    },
    required: ["cardId", "toColumn"]
  }
}
```

### 4. `update_card`

Update card properties:

```javascript
{
  name: "update_card",
  description: "Updates a card's title, description, priority, or labels",
  inputSchema: {
    type: "object",
    properties: {
      cardId: { type: "string" },
      title: { type: "string" },
      description: { type: "string" },
      priority: { type: "string" }
    },
    required: ["cardId"]
  }
}
```

### 5. `delete_card`

Delete a card from the board:

```javascript
{
  name: "delete_card",
  description: "Removes a card from the board",
  inputSchema: {
    type: "object",
    properties: {
      cardId: { type: "string", description: "Card ID to delete" }
    },
    required: ["cardId"]
  },
  annotations: { destructiveHint: true }
}
```

### 6. `add_label`

Add a label to an existing card:

```javascript
{
  name: "add_label",
  description: "Adds a label to an existing card (duplicates ignored)",
  inputSchema: {
    type: "object",
    properties: {
      cardId: { type: "string", description: "Card ID" },
      label: { type: "string", description: "Label to add" }
    },
    required: ["cardId", "label"]
  }
}
```

### 7. `get_column_summary`

Get a column's card count, priority breakdown, and card list:

```javascript
{
  name: "get_column_summary",
  description: "Returns a column's card count, priority breakdown, and card list",
  inputSchema: {
    type: "object",
    properties: {
      column: { type: "string", description: "Column name" }
    },
    required: ["column"]
  },
  annotations: { readOnlyHint: true }
}
```

### 8. `prioritize_column`

Reorder cards within a column by priority:

```javascript
{
  name: "prioritize_column",
  description: "Reorders cards within a column by priority (critical first)",
  inputSchema: {
    type: "object",
    properties: {
      column: { type: "string", description: "Column to prioritize" }
    },
    required: ["column"]
  }
}
```

## Features

| Feature | Description |
|---------|-------------|
| **Drag-and-drop** | Native HTML5 Drag and Drop for human card management |
| **8 WebMCP tools** | Full CRUD + labels + prioritization via AI |
| **localStorage** | Board state persists across sessions |
| **@mcp-b/global polyfill** | Uses the official WebMCP polyfill |
| **@mcp-b/react-webmcp** | React hooks (useWebMCP) for tool registration |
| **Zod validation** | Tool parameter validation with Zod schemas |
| **Live demo** | Deployed on Vercel |
| **Claude integration** | Pre-configured for Claude Desktop/Code via `@mcp-b/chrome-devtools-mcp` |

## Installation

```bash
git clone https://github.com/grzetich/webmcp-kanban.git
cd webmcp-kanban
npm install
npm run dev
# Open http://localhost:5173
```

## AI Interaction Example

```
User: "Create a high-priority bug card for the login page crash"

AI Agent:
  → create_card({
      title: "Fix login page crash",
      description: "Users experiencing crashes on the login page",
      column: "To Do",
      priority: "high"
    })
  → Card created

User: "Prioritize the To Do column"

AI Agent:
  → prioritize_column({ column: "To Do" })
  → Cards reordered by priority (critical first)
```

## See Also

- [Travel Demo](./travel-demo.md) — Official Chrome team demo
- [DoorDash Starter](./doordash-starter.md) — Single-file zero-dependency demo
- [Calendar Demo](./calendar-demo.md) — Next.js calendar with 15 WebMCP tools
- [@mcp-b/react-webmcp](../frameworks/react-webmcp.md) — React hooks for WebMCP
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Execute callback](../../02-specification/execute-callback.md) — Execute callback
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Enterprise workflows use case](../../11-use-cases/enterprise-workflows.md) — Enterprise workflows use case
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Human-in-the-loop patterns](../../06-user-interaction/human-in-the-loop.md) — Human-in-the-loop patterns
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
