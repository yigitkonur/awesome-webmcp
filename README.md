# 🚀 awesome-webmcp

> A curated list of resources for WebMCP — the W3C web standard that lets websites expose structured tools to AI agents via `navigator.modelContext`.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Chrome 146+](https://img.shields.io/badge/Chrome-146+-blue.svg)](https://developer.chrome.com/blog/webmcp-epp)
[![W3C Draft](https://img.shields.io/badge/W3C-Community%20Draft-green.svg)](https://webmachinelearning.github.io/webmcp)

---

## Contents

* [Specification & Proposals](#specification--proposals)
* [Browser Support](#browser-support)
* [Official Tooling](#official-tooling)
* [Polyfills & Core Libraries](#polyfills--core-libraries)
* [Framework Libraries](#framework-libraries)
* [Browser Extensions](#browser-extensions)
* [Bridges & Adapters](#bridges--adapters)
* [CMS Plugins](#cms-plugins)
* [Demo Applications](#demo-applications)
* [Starter Templates & Courses](#starter-templates--courses)
* [Articles & Tutorials](#articles--tutorials)
* [Videos & Talks](#videos--talks)
* [Security Resources](#security-resources)
* [Community](#community)
* [API Quick Reference](#api-quick-reference)
* [Key People](#key-people)

---

## Specification & Proposals

* **[W3C WebMCP Specification](https://webmachinelearning.github.io/webmcp)** — Normative spec draft. Defines `navigator.modelContext`, `registerTool()`, `provideContext()`, declarative HTML form attributes, events, and CSS pseudo-classes.
* **[webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp)** `948★` `81 commits` — Official repo. Bikeshed spec source, explainers, security/privacy docs, and issue tracker.
* **[Chrome WebMCP Early Preview](https://developer.chrome.com/blog/webmcp-epp)** — Chrome for Developers blog with setup instructions, API overview, and demo walkthrough.
* **[Service Workers Explainer](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md)** — Background tool execution without a visible tab. JIT installation, session management, discovery.
* **[Security & Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md)** — Official threat model: prompt injection, misrepresentation, over-parameterization.
* **[Accessibility Considerations (Issue #65)](https://github.com/webmachinelearning/webmcp/issues/65)** — WebMCP's potential to improve accessibility through agentic interfaces.
* **[In-Page Agent API (Issue #51)](https://github.com/webmachinelearning/webmcp/issues/51)** — Defining API for agents embedded in the page to consume declared tools.
* **[Scope Clarification (Issue #43)](https://github.com/webmachinelearning/webmcp/issues/43)** — What WebMCP covers vs. what belongs to the agent layer.

---

## Browser Support

| Browser | Status | Version | How to Enable |
|---|---|---|---|
| **Chrome** | ✅ Early Preview | 146.0.7672.0+ (Canary) | `chrome://flags` → "WebMCP for testing" |
| **Edge** | 🔜 Expected | TBD | Same Chromium flag |
| **Firefox** | ❌ Not yet | — | — |
| **Safari** | ❌ Not yet | — | — |

```javascript
if ("modelContext" in navigator) {
  // WebMCP is supported
}
```

---

## Official Tooling

* **[googlechromelabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools)** `27★` `TypeScript` — Official Chrome Labs toolkit. Includes Model Context Tool Inspector extension, WebMCP Evals CLI, React flight-search demo (imperative), and restaurant reservation demo (declarative).
* **[Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd)** — Official Chrome extension. List registered tools, execute manually, test with Gemini API.
* **[webmcp-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart)** `20★` `27 commits` — Quickstart for connecting Chrome DevTools MCP to WebMCP tools. Includes benchmark showing ~89% token reduction vs. screenshot automation.
* **[webmcp-org/webmcp-sh](https://github.com/WebMCP-org/webmcp-sh)** `7★` `152 commits` — Source code for [webmcp.sh](https://webmcp.sh/) playground. React 19, Vite, PGlite (in-browser Postgres), Cloudflare Worker.

---

## Polyfills & Core Libraries

* **[@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global)** — Polyfill implementing `navigator.modelContext` for browsers without native support. Drop-in replacement, same API surface as the spec.
* **[miguelspizza/webmcp](https://github.com/MiguelsPizza/WebMCP)** `982★` `160+ commits` `TypeScript` — MCP-B reference implementation. Monorepo with Chrome extension, tab/extension transports, native-server bridge, Zod validation. Published npm packages. Now continued under WebMCP-org.
* **[ripulio/web-mcp](https://github.com/ripulio/web-mcp)** `2★` `109 commits` `TypeScript` — Monorepo implementing WebMCP: MCP server CLI, Chrome extension, DevTools panel, and `navigator.modelContext` polyfill.
* **[opentiny/next-sdk](https://github.com/opentiny/next-sdk)** `6★` `300+ commits` `TypeScript` — Frontend AI SDK with `WebMcpServer`/`WebMcpClient` classes, transport adapters (MessageChannel, SSE, HTTP), Zod validation, and Vue 3 chat UI component.
* **[jasonjmcghee/webmcp](https://github.com/jasonjmcghee/WebMCP)** `404★` `90 commits` `JavaScript` — Early WebMCP prototype. Client-side widget + localhost WebSocket bridge. NPM package, Docker support, live demo at webmcp.dev. **Note:** Pre-dates the W3C spec; not spec-compliant.

---

## Framework Libraries

* **[@mcp-b/react-webmcp](https://www.npmjs.com/package/@mcp-b/react-webmcp)** — React hooks (`useWebMCP`, `useMcpTool`) for registering and invoking WebMCP tools.

---

## Browser Extensions

* **[igrigorik/agentboard](https://github.com/igrigorik/AgentBoard)** `106★` `TypeScript` — AI switchboard extension. Multi-provider LLM sidebar (OpenAI, Anthropic, Google, Ollama), scriptable WebMCP tools running in page context, remote MCP server support, command templates.
* **[webmcp-org/char-plugin](https://github.com/WebMCP-org/char-plugin)** `12 commits` `JavaScript` — Claude Code plugin. Installs Char embeddable AI agent, configures WebMCP servers, registers tools, provides `/char:setup` wizard.

---

## Bridges & Adapters

* **[nathan-gage/webmcp-bridge](https://github.com/nathan-gage/webmcp-bridge)** `49 commits` `TypeScript` — CLI + Chrome extension that bridges `navigator.modelContext` tools to any MCP client (Claude, Cursor, Windsurf) via local WebSocket. Plugin marketplace for injecting bridge scripts on sites without native WebMCP.
* **[victorzxj/webmcp-adapter](https://github.com/victorzxj/webmcp-adapter)** `JavaScript` — Universal adapter exposing frontend page actions as WebMCP tools with auto-discovery, schema generation, and Service Worker governance. *(Early stage)*
* **[6missedcalls/openclaw-webmcp-skill](https://github.com/6missedcalls/openclaw-webmcp-skill)** `JavaScript` — OpenClaw skill enabling AI agents to discover and invoke `navigator.modelContext` tools without DOM scraping.

---

## CMS Plugins

* **[wmcp.dev](https://www.wmcp.dev/)** — WordPress plugin adding WebMCP declarative attributes (`toolname`, `tooldescription`) to Contact Form 7, Gravity Forms, WPForms, and WooCommerce forms.
* **[chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect)** `PHP` — WordPress plugin exposing WebMCP REST API. AI agents authenticate via JWT and invoke tools like `wordpress.searchPosts`, `wordpress.getPost`.
* **[tuvit/webmcp](https://github.com/tuvit/webmcp)** `TypeScript` — Wix platform extension injecting WebMCP attributes into Wix Stores pages for AI agent access to e-commerce data.

---

## Demo Applications

### Substantial Demos
* **[Travel Demo](https://travel-demo.bandarra.me/)** — Official Chrome team demo. `searchFlights` tool via imperative API. Source: part of the Chrome Early Preview docs.
* **[Playground WebMCP](https://webmcp.sh/)** — Interactive browser playground. Connect to MCP servers, run tools, in-browser PostgreSQL. ([Source](https://github.com/WebMCP-org/webmcp-sh))
* **[grzetich/webmcp-kanban](https://github.com/grzetich/webmcp-kanban)** `8 commits` `JavaScript` — React kanban board with 8 AI-callable tools (create, move, update, delete cards). Drag-and-drop, localStorage persistence. [Live demo](https://webmcp-kanban-demo.vercel.app).
* **[webmcp-org/big-calendar](https://github.com/WebMCP-org/big-calendar)** `169 commits` `TypeScript` — Next.js calendar with 15 WebMCP tools for CRUD, navigation, and settings. Embedded AI agent via chat widget.
* **[webmcp-org/ai-tinkerers-webmcp-demo](https://github.com/WebMCP-org/ai-tinkerers-webmcp-demo)** `TypeScript` — In-browser RAG pipeline: document ingestion, Transformers.js embeddings, Dexie/IndexedDB storage, semantic search — all driven by AI agent via WebMCP tools.
* **[legit-control/webmcp-exploration](https://github.com/Legit-Control/webMCP-exploration)** `3★` `14 commits` `TypeScript` — React app integrating WebMCP with Legit SDK for Git-like versioned state mutations. AI agents edit calendar in isolated branches with visual previews.
* **[srinivasan target/webmcp-demo](https://github.com/SrinivasanTarget/WebMCP-demo)** `4 commits` `JavaScript` — Full WebMCP polyfill demo with 15+ sample tools (math, DOM, storage, network) and an AI-agent simulator with JSON-RPC messaging.
* **[tsunoyu/webmcp-travel-insurance](https://github.com/tsunoyu/webmcp-travel-insurance)** `5 commits` `JavaScript` — Travel insurance app demonstrating complete policy lifecycle (quoting, purchasing, claims) via `navigator.modelContext` tools.
* **[marisviana/webmcp_demo-site](https://github.com/marisviana/webmcp_demo-site)** — E-commerce demo with WebMCP tools for product filtering and cart management.
* **[ripulio/webmcp-tools](https://github.com/ripulio/webmcp-tools)** `2★` `59 commits` `TypeScript` — Catalog of production-ready WebMCP tool definitions with schemas, metadata, and build pipeline.

### Minimal Demos
* **[khushalsagar/webmcp-demo](https://github.com/khushalsagar/webmcp-demo)** `3★` `JavaScript` — By the Chrome spec co-author. Flight booking with both declarative HTML and imperative JS approaches.
* **[doriandarko/webmcp-starter](https://github.com/Doriandarko/webmcp-starter)** `13★` `HTML` — Single-file "Midnight Eats" DoorDash-style demo. 9 tools (8 imperative, 1 declarative). Zero dependencies.
* **[alecron/webmcp-demo](https://github.com/alecron/webmcp-demo)** `JavaScript` — Notes app with 5 tools (add, list, search, delete, stats) using `navigator.modelContext.provideContext()`.
* **[best-seller/webmcp-demo](https://github.com/best-seller/webmcp-demo)** — Imperative + declarative API examples for AI agent interaction.
* **[alanw707/webmcp-readiness-radar](https://github.com/alanw707/webmcp-readiness-radar)** `JavaScript` — Web app scoring a site's WebMCP agent-readiness with pillar-wise breakdown and recommendations.

---

## Starter Templates & Courses

* **[codelytv/webmcp-course](https://github.com/CodelyTV/webmcp-course)** `18 commits` `HTML/JS` — Course examples for both declarative (`toolname`/`tooldescription` attributes) and imperative (`navigator.modelContext`) APIs. Tied to Codely Pro Spanish course.
* **[lemonhall/webmcp_readme](https://github.com/lemonhall/webmcp_readme)** — Comprehensive guide and explanation of WebMCP with practical React flight search demo.

---

## Articles & Tutorials

### Overviews
* **[Chrome's WebMCP Early Preview: The End of AI Agents Clicking Buttons](https://dev.to/axrisi/chromes-webmcp-early-preview-the-end-of-ai-agents-clicking-buttons-b6e)** — Most comprehensive third-party walkthrough. Both APIs, events, CSS pseudo-classes, best practices.
* **[What is WebMCP? Your Website Just Became a Function Call](https://www.nohackspod.com/blog/what-is-webmcp)** — Clean explainer comparing WebMCP vs server-side approaches, covering declarative HTML.
* **[WebMCP (Dejan AI)](https://dejan.ai/blog/webmcp/)** — SEO-focused: tool descriptions as the next indexing problem — ranked by LLMs like meta descriptions by search engines.
* **[WebMCP: Making the Web AI-Agent Ready](https://dev.to/sunny7899/webmcp-making-the-web-ai-agent-ready-5152)** — Introduction with code samples for `registerTool`, `provideContext`, and declarative forms.

### Technical Deep-Dives
* **[Chrome's WebMCP Makes AI Agents Stop Pretending](https://medium.com/reading-sh/chromes-webmcp-makes-ai-agents-stop-pretending-e8c7da1ba650)** — Trust model and security analysis. Notes 51% of web traffic is bots.
* **[Google Chrome Ships WebMCP (VentureBeat)](https://venturebeat.com/infrastructure/google-chrome-ships-webmcp-in-early-preview-turning-every-website-into-a)** — Industry coverage with Google/Microsoft engineer quotes.
* **[Google Previews WebMCP (Search Engine Land)](https://searchengineland.com/google-releases-preview-of-webmcp-how-ai-agents-interact-with-websites-469024)** — "Is this the next frontier of technical SEO?"
* **[Google's WebMCP Moves the Web Closer to a Structured Database (The Decoder)](https://the-decoder.com/googles-webmcp-moves-the-web-closer-to-becoming-a-structured-database-for-ai-agents/)** — Broader web architecture implications.
* **[How WebMCP Lets Developers Control AI Agents With JavaScript (The New Stack)](https://thenewstack.io/how-webmcp-lets-developers-control-ai-agents-with-javascript/)** — Developer-focused technical analysis.
* **[The WebMCP Revolution: Transforming SEO (xugj520)](https://www.xugj520.cn/en/archives/webmcp-seo-agents-capability-indexing.html)** — How WebMCP shifts SEO from content to capability indexing.

### Interviews
* **[WebMCP: Making Every Website a Tool — Alex Nahas Interview (Arcade.dev)](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview)** — Amazon engineer who built MCP-B, inspiring the W3C proposal.

### Quick Starts
* **[WebMCP Just Landed in Chrome 146 (bug0.com)](https://bug0.com/blog/webmcp-chrome-146-guide)** — Concise setup tutorial.
* **[I Added 3 WebMCP Attributes (Medium)](https://medium.com/@harshiljani2002/i-have-added-3-webmcp-attributes-to-a-form-and-ai-agents-started-using-my-website-like-an-api-175231179545)** — Declarative approach walkthrough.

### Agentic Web Context
* **[MCP, A2A, NLWeb, and AGENTS.md (No Hacks Pod)](https://www.nohackspod.com/blog/agentic-web-protocols)** — Where WebMCP fits among agentic protocols.
* **[The Agentic Web (The New Stack)](https://thenewstack.io/the-agentic-web-how-ai-agents-are-shaping-the-webs-future/)** — W3C perspective including WebMCP.
* **[Inside the Agentic Web (All Things Open)](https://allthingsopen.org/articles/inside-the-agentic-web-what-developers-should-know)** — Developer standards landscape overview.

---

## Videos & Talks

* **[Don't Let AI Agents Push Your Buttons — Use WebMCP Instead!](https://www.youtube.com/watch?v=p1l8nkQAoUw)** — Khushal Sagar (Chrome Staff SWE, spec co-author). Nov 2025.
* **[The Rise of WebMCP](https://www.youtube.com/watch?v=35oWt7u2b-g)** — Sam Witteveen. Launch coverage and web dev impact.

---

## Security Resources

### Official Threat Model
The [Security and Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) covers:

1. **Prompt Injection** — Tool poisoning (malicious descriptions), output injection (tainted return values), tool-as-target (exploiting high-value tools)
2. **Misrepresentation** — Tools whose behavior doesn't match their description
3. **Privacy Leakage** — Over-parameterized tools extracting unnecessary personal data

### External Security Guides
* **[MCP-B Security Best Practices](https://docs.mcp-b.ai/security)** — Practical recommendations for WebMCP implementations
* **[Known Security Issues (MiguelsPizza Wiki)](https://github.com/MiguelsPizza/WebMCP/wiki/Known-Security-Issues-With-WebMCP)** — Community-maintained attack vector list
* **[Protecting Against Indirect Injection (Microsoft)](https://developer.microsoft.com/blog/protecting-against-indirect-injection-attacks-mcp)** — Applicable to WebMCP tool descriptions
* **[Mitigating Prompt Injections in Browser Use (Anthropic)](https://www.anthropic.com/research/prompt-injection-defenses)** — Defense strategies for browser-based AI agents

### Developer Checklist

```
✅ Validate all input in execute() — don't rely on schema alone
✅ Use agent.requestUserInteraction() for destructive/financial actions
✅ Check event.agentInvoked to differentiate agent vs human
✅ Set readOnlyHint: true on read-only tools
✅ Never return raw PII in tool responses
✅ Implement rate limiting in tool handlers
✅ Keep descriptions positive ("Search flights") not negative ("Don't use for hotels")
✅ Log all tool invocations for audit
✅ Update UI after execution — agents verify state via DOM
✅ Use toolautosubmit only for idempotent operations
```

---

## Community

* **[Chrome AI Dev Preview Group](https://groups.google.com/a/chromium.org/g/chrome-ai-dev-preview-discuss/)** — Official Google discussion
* **[GitHub Issues](https://github.com/webmachinelearning/webmcp/issues)** — Spec feedback
* **[Bug Reports](https://crbug.com/new?component=2021259)** — Chrome implementation bugs
* **[r/HowToAIAgent](https://www.reddit.com/r/HowToAIAgent/comments/1r36bec/webmcp_just_dropped_in_chrome_146_and_now_your/)** — Chrome 146 launch discussion
* **[r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1r2m8cw/chromes_webmcp_makes_ai_agents_stop_pretending/)** — Developer reactions

---

## API Quick Reference

### Imperative API
```javascript
// Register a single tool
navigator.modelContext.registerTool({
  name: "searchFlights",
  description: "Search for available flights between two airports",
  inputSchema: {
    type: "object",
    properties: {
      origin:      { type: "string", description: "IATA code (e.g. LHR)" },
      destination: { type: "string", description: "IATA code (e.g. JFK)" },
      date:        { type: "string", format: "date" }
    },
    required: ["origin", "destination", "date"]
  },
  annotations: { readOnlyHint: true },
  execute: async ({ origin, destination, date }) => {
    const results = await searchFlightsAPI(origin, destination, date);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});

// Replace all tools
navigator.modelContext.provideContext({ tools: [/* ... */] });

// Remove one tool / all tools
navigator.modelContext.unregisterTool("searchFlights");
navigator.modelContext.clearContext();
```

### Declarative API
```html
<form toolname="book_table"
      tooldescription="Reserve a table at this restaurant"
      toolautosubmit action="/api/reserve">
  <input type="date" name="date" required toolparamdescription="Reservation date">
  <input type="time" name="time" required toolparamdescription="Reservation time">
  <input type="number" name="guests" min="1" max="20" required>
  <button type="submit">Reserve</button>
</form>
```

### Events & CSS
```javascript
form.addEventListener("submit", (e) => {
  if (e.agentInvoked) {
    e.preventDefault();
    e.respondWith(handleAgentSubmission(e));
  }
});

window.addEventListener('toolactivated', ({ toolName }) => { /* ... */ });
window.addEventListener('toolcancel', ({ toolName }) => { /* ... */ });
```

```css
form:tool-form-active { outline: 2px solid #4CAF50; }
button:tool-submit-active { background: #FFD700; }
```

### User Interaction
```javascript
execute: async ({ productId }, agent) => {
  const ok = await agent.requestUserInteraction(async () => {
    return confirm(`Purchase ${productId}?`);
  });
  if (!ok) throw new Error("Canceled");
  return { content: [{ type: "text", text: "Purchased" }] };
}
```

---

## Key People

| Person | Affiliation | Role | Links |
|---|---|---|---|
| **Brandon Walderman** | Microsoft | Spec co-author | [LinkedIn](https://www.linkedin.com/in/brandonwalderman) |
| **Andrew Nolan** | Microsoft | Spec co-author | [LinkedIn](https://www.linkedin.com/in/andrewnolanaiproductleader) |
| **David Bokan** | Google | Spec co-author | — |
| **Khushal Sagar** | Google | Spec co-author, Chrome Staff SWE | [LinkedIn](https://www.linkedin.com/in/khushal-sagar) |
| **Hannah Van Opstal** | Google | Spec co-author | [LinkedIn](https://ca.linkedin.com/in/hannah-van-opstal) |
| **Dominic Farolino** | Google | Spec editor | [LinkedIn](https://www.linkedin.com/in/domfarolino) · [X](https://x.com/domfarolino) |
| **Alexandra Klepper** | Google | Chrome Early Preview author | [LinkedIn](https://www.linkedin.com/in/alexandraklepper) · [Mastodon](https://mstdn.social/@alexandrawhite) |
| **François Beaufort** | Google | Chrome Early Preview author | [X](https://x.com/quicksave2k) |
| **André Bandarra** | Google | Chrome Early Preview author, travel demo | [LinkedIn](https://uk.linkedin.com/in/andreban) · [X](https://x.com/andreban) |
| **Alex Nahas** | Amazon (prev.) | Original WebMCP concept, MCP-B creator | [LinkedIn](https://www.linkedin.com/in/alex-nahas) |
| **Jason McGhee** | Independent | Early WebMCP proposal | [LinkedIn](https://www.linkedin.com/in/jasonjmcghee) · [X](https://x.com/_jason_today) |

---

## Contributing

Only submit resources **directly about the WebMCP browser standard** (`navigator.modelContext` API or `toolname`/`tooldescription` HTML attributes). Do not submit general server-side MCP resources.

---

## Maintainers

* Yiğit Konur [@yigitkonur](https://x.com/yigitkonur)
* Web-MCP.net Crew [@web-mcp.net](https://web-mcp.net)
