# GoogleChromeLabs/webmcp-tools

> Official Chrome Labs toolkit for developing, testing, and evaluating WebMCP tools.

## Overview

[GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) is the **official toolkit** published by Google's Chrome Labs team for the WebMCP ecosystem. It provides the core developer utilities needed to build, inspect, and evaluate WebMCP tool implementations. The repository is a monorepo containing multiple packages that work together to streamline the WebMCP development workflow.

- **Repository**: [github.com/GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools)
- **License**: Apache-2.0
- **Stars**: ~56 (as of Feb 2026)
- **Maintained by**: Google Chrome Labs

## Components

### 1. Model Context Tool Inspector (Chrome Extension)

A Chrome extension for listing, inspecting, and manually executing WebMCP tools registered on any page. See [model-context-tool-inspector.md](./model-context-tool-inspector.md) for full details.

- Lists all tools registered via `navigator.modelContext.registerTool()` and declarative `toolname` attributes
- Manual tool execution with parameter entry
- Built-in testing with Gemini API integration
- Available on the [Chrome Web Store](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)

### 2. WebMCP Evals CLI

A command-line tool for evaluating WebMCP tool quality and effectiveness. See [webmcp-evals-cli.md](./webmcp-evals-cli.md) for full details.

- Automated evaluation of tool descriptions, schemas, and execution behavior
- Quality scoring and recommendations
- CI/CD integration support

### 3. Flight Search Demo (Imperative API)

A React-based demo application showcasing the imperative `navigator.modelContext` API with a flight search tool.

- **Live demo**: [flight-search.firebaseapp.com](https://flight-search.firebaseapp.com)
- Implements `searchFlights` tool using the [imperative API](https://webmachinelearning.github.io/webmcp)
- Demonstrates proper `registerTool()` usage with JSON Schema input validation
- Shows real-time UI updates when tools are invoked by an AI agent

```javascript
// Example from the flight-search demo
navigator.modelContext.registerTool({
  name: "searchFlights",
  description: "Search for available flights between airports",
  inputSchema: {
    type: "object",
    properties: {
      origin: { type: "string", description: "IATA airport code" },
      destination: { type: "string", description: "IATA airport code" },
      date: { type: "string", format: "date" }
    },
    required: ["origin", "destination", "date"]
  },
  execute: async ({ origin, destination, date }) => {
    const results = await searchFlightsAPI(origin, destination, date);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});
```

### 4. Restaurant Reservation Demo (Declarative API)

A demo showcasing the [declarative HTML API](https://github.com/webmachinelearning/webmcp/pull/76) for tool registration using form attributes.

- Uses `toolname`, `tooldescription`, and `toolparamdescription` HTML attributes
- No JavaScript required for tool registration
- Form submission handled automatically by the browser
- Demonstrates `toolautosubmit` for idempotent operations

```html
<form toolname="reserve_table"
      tooldescription="Reserve a table at this restaurant"
      action="/api/reserve">
  <input type="date" name="date" required
         toolparamdescription="Desired reservation date">
  <input type="time" name="time" required
         toolparamdescription="Preferred time">
  <input type="number" name="guests" min="1" max="20" required
         toolparamdescription="Number of guests">
  <button type="submit">Reserve</button>
</form>
```

## Installation & Usage

### Clone the Repository

```bash
git clone https://github.com/GoogleChromeLabs/webmcp-tools.git
cd webmcp-tools
```

### Run a Demo Locally

```bash
# Install dependencies
npm install

# Start the flight-search demo
cd demos/flight-search
npm start
```

### Install the Chrome Extension

1. Visit the [Chrome Web Store listing](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)
2. Click "Add to Chrome"
3. Navigate to any page with WebMCP tools
4. Open the extension to see registered tools

## Architecture

```
webmcp-tools/
├── extension/           # Model Context Tool Inspector Chrome extension
│   ├── manifest.json
│   ├── popup/           # Extension popup UI
│   ├── devtools/        # DevTools panel integration
│   └── content/         # Content scripts for tool discovery
├── cli/                 # WebMCP Evals CLI
│   ├── src/
│   └── package.json
├── demos/
│   ├── flight-search/   # Imperative API demo (React)
│   └── restaurant/      # Declarative API demo (HTML forms)
└── README.md
```

## Key Features

| Feature | Description |
|---------|-------------|
| **Tool Inspector** | Visual inspection of registered tools, schemas, and annotations |
| **Manual Execution** | Execute tools with custom parameters directly from the extension |
| **Gemini Testing** | Test tool interactions using Google's Gemini API |
| **Evals CLI** | Automated quality evaluation for tool descriptions and schemas |
| **Imperative Demo** | Reference implementation using `navigator.modelContext.registerTool()` |
| **Declarative Demo** | Reference implementation using HTML `toolname` attributes |

## Requirements

- Chrome 146+ (Canary) with `chrome://flags` → "WebMCP for testing" enabled
- Node.js 18+ for CLI tools and demo development
- npm or pnpm for package management

## See Also

- [Model Context Tool Inspector](./model-context-tool-inspector.md) — Detailed extension documentation
- [WebMCP Evals CLI](./webmcp-evals-cli.md) — CLI evaluation tool documentation
- [DevTools Quickstart](./devtools-quickstart.md) — Chrome DevTools MCP integration
- [Travel Demo](../demos/travel-demo.md) — Official Chrome team travel demo
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Chrome Early Preview Blog](https://developer.chrome.com/blog/webmcp-epp) — Setup instructions
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Specification overview](../../02-specification/spec-overview.md) — Specification overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Security checklist for tool developers](../../04-security/developer-security-checklist.md) — Security checklist for tool developers
- [Spec co-editor involved in Chrome Labs](../../09-people/brandon-walderman.md) — Spec co-editor involved in Chrome Labs
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Testing tools and evaluation](../../12-tutorials/testing-tools.md) — Testing tools and evaluation
- [Google Chrome's role in WebMCP](../../14-industry/google-chrome-involvement.md) — Google Chrome's role in WebMCP
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
