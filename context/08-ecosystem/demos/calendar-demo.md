# WebMCP-org/big-calendar

> Next.js calendar application with 15 WebMCP tools for CRUD, navigation, and settings. Includes embedded AI agent via chat widget.

## Overview

[WebMCP-org/big-calendar](https://github.com/WebMCP-org/big-calendar) is a Next.js-based calendar application that registers 15 WebMCP tools, providing comprehensive AI agent access to calendar operations including event CRUD, navigation, view switching, and settings management. It includes an embedded AI agent chat widget powered by the [Char plugin](https://github.com/WebMCP-org/char-plugin).

- **Repository**: [github.com/WebMCP-org/big-calendar](https://github.com/WebMCP-org/big-calendar)
- **Maintained by**: WebMCP-org
- **Framework**: Next.js 14+
- **License**: MIT

## WebMCP Tools (15 Total)

### Event CRUD (5 tools)

| # | Tool | Description | Annotations |
|---|------|-------------|-------------|
| 1 | `calendar.createEvent` | Create a new calendar event | — |
| 2 | `calendar.updateEvent` | Update an existing event | `idempotentHint: true` |
| 3 | `calendar.deleteEvent` | Delete a calendar event | `destructiveHint: true` |
| 4 | `calendar.getEvent` | Get details of a specific event | `readOnlyHint: true` |
| 5 | `calendar.listEvents` | List events in a date range | `readOnlyHint: true` |

### Navigation (4 tools)

| # | Tool | Description | Annotations |
|---|------|-------------|-------------|
| 6 | `calendar.goToDate` | Navigate to a specific date | — |
| 7 | `calendar.goToToday` | Navigate to today | — |
| 8 | `calendar.goForward` | Navigate forward (day/week/month) | — |
| 9 | `calendar.goBack` | Navigate backward | — |

### View Management (3 tools)

| # | Tool | Description | Annotations |
|---|------|-------------|-------------|
| 10 | `calendar.setView` | Switch between day/week/month/agenda | `idempotentHint: true` |
| 11 | `calendar.getView` | Get current view mode | `readOnlyHint: true` |
| 12 | `calendar.setDateRange` | Set visible date range | — |

### Search & Settings (3 tools)

| # | Tool | Description | Annotations |
|---|------|-------------|-------------|
| 13 | `calendar.searchEvents` | Search events by keyword | `readOnlyHint: true` |
| 14 | `calendar.getSettings` | Get calendar settings | `readOnlyHint: true` |
| 15 | `calendar.updateSettings` | Update calendar settings | `idempotentHint: true` |

## Tool Schemas (Key Examples)

### `calendar.createEvent`

```javascript
{
  name: "calendar.createEvent",
  description: "Create a new event on the calendar",
  inputSchema: {
    type: "object",
    properties: {
      title: { type: "string", description: "Event title" },
      start: { type: "string", format: "date-time", description: "Start date/time (ISO 8601)" },
      end: { type: "string", format: "date-time", description: "End date/time (ISO 8601)" },
      description: { type: "string", description: "Event description" },
      location: { type: "string", description: "Event location" },
      color: { type: "string", description: "Event color (hex code)" },
      allDay: { type: "boolean", description: "Whether this is an all-day event" },
      recurring: {
        type: "object",
        properties: {
          frequency: { type: "string", enum: ["daily", "weekly", "monthly", "yearly"] },
          count: { type: "number", description: "Number of occurrences" }
        }
      }
    },
    required: ["title", "start", "end"]
  }
}
```

### `calendar.searchEvents`

```javascript
{
  name: "calendar.searchEvents",
  description: "Search calendar events by keyword, date range, or location",
  inputSchema: {
    type: "object",
    properties: {
      query: { type: "string", description: "Search keyword" },
      startDate: { type: "string", format: "date" },
      endDate: { type: "string", format: "date" },
      location: { type: "string" }
    },
    required: ["query"]
  },
  annotations: { readOnlyHint: true }
}
```

## Embedded AI Agent (Char Widget)

The calendar includes an embedded AI chat widget via the [Char plugin](https://github.com/WebMCP-org/char-plugin):

```javascript
// AI agent can interact with all 15 calendar tools
// Embedded as a floating chat widget in the corner

// Example conversation:
// User: "Schedule a team standup every Monday at 9 AM"
// AI → calendar.createEvent({
//   title: "Team Standup",
//   start: "2026-03-23T09:00:00",
//   end: "2026-03-23T09:30:00",
//   recurring: { frequency: "weekly", count: 52 }
// })
```

## Installation

```bash
git clone https://github.com/WebMCP-org/big-calendar.git
cd big-calendar
npm install
npm run dev
# Open http://localhost:3000
```

## Tech Stack

| Component | Technology |
|-----------|-----------|
| **Framework** | Next.js 14 (App Router) |
| **Calendar** | react-big-calendar |
| **State** | React Context + useReducer |
| **Storage** | localStorage (or backend API) |
| **Styling** | Tailwind CSS |
| **AI Agent** | Char embeddable widget |
| **WebMCP** | Native API + @mcp-b/global polyfill |

## AI Interaction Example

```
User: "What do I have scheduled this week?"

AI Agent:
  → calendar.listEvents({
      start: "2026-03-16",
      end: "2026-03-22"
    })
  → 5 events found:
    Mon: Team Standup (9:00-9:30)
    Tue: Design Review (14:00-15:00)
    Wed: Sprint Planning (10:00-11:30)
    Thu: Client Call (16:00-16:30)
    Fri: Retrospective (15:00-16:00)

User: "Move the client call to next Tuesday at 2pm"

AI Agent:
  → calendar.updateEvent({
      eventId: "evt-client-call",
      start: "2026-03-24T14:00:00",
      end: "2026-03-24T14:30:00"
    })
  → Event updated successfully
```

## See Also

- [Kanban Demo](./kanban-demo.md) — React kanban with 8 WebMCP tools
- [Travel Demo](./travel-demo.md) — Official Chrome team demo
- [DoorDash Starter](./doordash-starter.md) — Single-file zero-dependency demo
- [Char Plugin](https://github.com/WebMCP-org/char-plugin) — Embedded AI agent
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Execute callback](../../02-specification/execute-callback.md) — Execute callback
- [Requesting user interaction](../../06-user-interaction/request-user-interaction.md) — Requesting user interaction
- [Enterprise workflows use case](../../11-use-cases/enterprise-workflows.md) — Enterprise workflows use case
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Human-in-the-loop patterns](../../06-user-interaction/human-in-the-loop.md) — Human-in-the-loop patterns
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
