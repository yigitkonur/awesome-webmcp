# Travel Demo — Official Chrome Team Demo

> Official Chrome team WebMCP demo featuring `searchFlights` tool via the imperative `navigator.modelContext` API.

## Overview

The [Travel Demo](https://travel-demo.bandarra.me/) is the **official Chrome team demonstration** of the WebMCP API, built by **André Cipriani Bandarra** ([@andreban](https://github.com/andreban)), a Google Chrome engineer. It showcases the imperative API for registering a `searchFlights` tool that AI agents can discover and invoke to search for flights.

- **Live Demo**: [travel-demo.bandarra.me](https://travel-demo.bandarra.me/)
- **Author**: André Cipriani Bandarra (Google Chrome team)
- **API Used**: [Imperative API](https://webmachinelearning.github.io/webmcp) (`navigator.modelContext.registerTool()`)
- **Part of**: Chrome Early Preview documentation (published February 10, 2026)

## What It Demonstrates

### Tool Registration

The demo registers a `searchFlights` tool using the imperative API:

```javascript
navigator.modelContext.registerTool({
  name: "searchFlights",
  description: "Search for available flights between two airports on a given date",
  inputSchema: {
    type: "object",
    properties: {
      origin: {
        type: "string",
        description: "Origin airport IATA code (e.g., LHR, JFK, SFO)"
      },
      destination: {
        type: "string",
        description: "Destination airport IATA code (e.g., NRT, CDG, LAX)"
      },
      date: {
        type: "string",
        format: "date",
        description: "Travel date in YYYY-MM-DD format"
      }
    },
    required: ["origin", "destination", "date"]
  },
  annotations: {
    readOnlyHint: true
  },
  execute: async ({ origin, destination, date }) => {
    // Simulates a flight search API call
    const results = await searchFlightsAPI(origin, destination, date);
    return {
      content: [{
        type: "text",
        text: JSON.stringify(results, null, 2)
      }]
    };
  }
});
```

### UI Updates on Tool Invocation

The demo shows proper UI patterns for WebMCP:

- **Loading state**: Shows a spinner when the tool is being executed
- **Results display**: Renders search results in a formatted list
- **Agent indicator**: Highlights when actions are initiated by an AI agent
- **CSS pseudo-classes**: Uses `:tool-form-active` for visual feedback

### Event Handling

```javascript
window.addEventListener('toolactivated', (event) => {
  const { toolName } = event;
  showToolActiveIndicator(toolName);
});

window.addEventListener('toolcancel', (event) => {
  const { toolName } = event;
  hideToolActiveIndicator(toolName);
});
```

## How to Use

### Prerequisites

1. Install Chrome 146+ (Canary)
2. Enable WebMCP: `chrome://flags` → "WebMCP for testing" → Enabled
3. Install the [Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)

### Steps

1. Navigate to [travel-demo.bandarra.me](https://travel-demo.bandarra.me/)
2. Open the Model Context Tool Inspector extension
3. See the `searchFlights` tool listed
4. Click to expand and view the schema
5. Enter test parameters (e.g., origin: "LHR", destination: "JFK", date: "2026-03-20")
6. Execute the tool and see results

### With an AI Agent

When using an AI agent (e.g., via Gemini in the Tool Inspector):

```
User: "Find me flights from San Francisco to Tokyo on April 15th"

AI Agent:
  → Discovers searchFlights tool on the page
  → Calls searchFlights({ origin: "SFO", destination: "NRT", date: "2026-04-15" })
  → Receives structured flight results
  → Presents formatted results to the user

UI:
  → Shows loading spinner during search
  → Displays flight results in the page
  → CSS :tool-form-active provides visual feedback
```

## Key Implementation Patterns

### Best Practices Demonstrated

| Practice | Implementation |
|----------|---------------|
| **Clear descriptions** | "Search for available flights between two airports" |
| **IATA code examples** | `description: "Origin airport IATA code (e.g., LHR, JFK)"` |
| **Date format hint** | `format: "date"` in the schema |
| **Read-only annotation** | `annotations: { readOnlyHint: true }` |
| **Structured response** | JSON content type with formatted results |
| **UI feedback** | Loading states, CSS pseudo-classes |

### Related Demo by Spec Co-Author

[khushalsagar/webmcp-demo](https://github.com/khushalsagar/webmcp-demo) by Chrome spec co-author Khushal Sagar demonstrates both:
- **Declarative HTML** approach using `toolname` attributes on forms
- **Imperative JavaScript** approach using `navigator.modelContext.registerTool()`

This provides a side-by-side comparison of both API styles for the same flight booking scenario.

## Source Code

The demo source is not open-sourced separately but is referenced in:
- [Chrome Early Preview blog post](https://developer.chrome.com/blog/webmcp-epp)
- [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) demos

## See Also

- [Chrome Labs Toolkit](../official-tools/chrome-labs-toolkit.md) — Official toolkit with flight-search demo
- [Model Context Tool Inspector](../official-tools/model-context-tool-inspector.md) — Tool for testing the demo
- [Playground (webmcp.sh)](./playground-webmcp-sh.md) — Interactive WebMCP playground
- [DoorDash Starter](./doordash-starter.md) — Single-file demo with zero dependencies
- [Chrome Early Preview](https://developer.chrome.com/blog/webmcp-epp) — Official setup guide
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Execute callback](../../02-specification/execute-callback.md) — Execute callback
- [Travel booking use case](../../11-use-cases/travel-booking.md) — Travel booking use case
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Spec editor and demo author](../../09-people/brandon-walderman.md) — Spec editor and demo author
- [Google Chrome's role](../../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
