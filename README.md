# Awesome WebMCP

> A curated list of resources for WebMCP — the W3C web standard that lets websites expose structured tools to AI agents via `navigator.modelContext`.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Chrome 146+](https://img.shields.io/badge/Chrome-146+-blue.svg)](https://developer.chrome.com/blog/webmcp-epp)
[![W3C Draft](https://img.shields.io/badge/W3C-Community%20Draft-green.svg)](https://webmachinelearning.github.io/webmcp)

## Contents

- [Official Tooling](#official-tooling)
- [Polyfills & Core Libraries](#polyfills--core-libraries)
- [Framework Libraries](#framework-libraries)
- [Browser Extensions](#browser-extensions)
- [Bridges & Adapters](#bridges--adapters)
- [CMS & Platform Integrations](#cms--platform-integrations)
- [Demo Applications](#demo-applications)
- [Starter Templates & Courses](#starter-templates--courses)
- [Articles & Tutorials](#articles--tutorials)
- [Videos & Talks](#videos--talks)
- [Security Resources](#security-resources)
- [Community](#community)
- [Specification](#specification)
- [API Quick Reference](#api-quick-reference)
- [Key People](#key-people)

---

## Official Tooling

- [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) - Official Chrome Labs toolkit with Model Context Tool Inspector extension, WebMCP Evals CLI, React [flight-search demo](https://flight-search.firebaseapp.com) ([imperative](https://webmachinelearning.github.io/webmcp)), and restaurant reservation demo ([declarative](https://github.com/webmachinelearning/webmcp/pull/76)).
- [Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd) - Official Chrome extension for listing registered tools, executing manually, and testing with Gemini API.
- [WebMCP-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart) - Quickstart for connecting Chrome DevTools MCP to WebMCP tools with benchmark showing ~89% token reduction vs. screenshot automation.
- [WebMCP-org/webmcp-sh](https://github.com/WebMCP-org/webmcp-sh) - Source code for [webmcp.sh](https://webmcp.sh/) playground built with React 19, Vite, PGlite (in-browser Postgres), and Cloudflare Workers.

## Polyfills & Core Libraries

- [@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global) - [Polyfill](https://webmachinelearning.github.io/webmcp) implementing [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) for browsers without native support. Drop-in replacement with the same API surface as the spec.
- [MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP) ([mcp-b.ai](https://mcp-b.ai)) - MCP-B reference implementation by [@MiguelsPizza](https://github.com/MiguelsPizza). Monorepo with Chrome extension, tab/extension transports, native-server bridge, and Zod validation. Now continued under WebMCP-org.
- [ripulio/web-mcp](https://github.com/ripulio/web-mcp) - Monorepo implementing WebMCP with MCP server CLI, Chrome extension, DevTools panel, and [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) polyfill.
- [opentiny/next-sdk](https://github.com/opentiny/next-sdk) - Frontend AI SDK with `WebMcpServer`/`WebMcpClient` classes, transport adapters (MessageChannel, SSE, HTTP), Zod validation, and Vue 3 chat UI component.
- [jasonjmcghee/WebMCP](https://github.com/jasonjmcghee/WebMCP) - Early WebMCP prototype by [@jasonjmcghee](https://github.com/jasonjmcghee) with client-side widget and localhost WebSocket bridge. NPM package and Docker support. Pre-dates the W3C spec; not spec-compliant.

## Framework Libraries

- [@mcp-b/react-webmcp](https://www.npmjs.com/package/@mcp-b/react-webmcp) - React hooks (`useWebMCP`, `useMcpTool`) for registering and invoking WebMCP tools.

## Browser Extensions

- [igrigorik/AgentBoard](https://github.com/igrigorik/AgentBoard) - AI switchboard extension by [@igrigorik](https://github.com/igrigorik) with multi-provider LLM sidebar (OpenAI, Anthropic, Google, Ollama), scriptable WebMCP tools running in page context, remote MCP server support, and command templates.
- [amedina/agentic-web-learning-tool](https://github.com/amedina/agentic-web-learning-tool) - Chrome extension framework for agentic AI workflows, visual workflow composition, MCP server integration, and Chrome built-in AI playground.
- [WebMCP-org/char-plugin](https://github.com/WebMCP-org/char-plugin) - Claude Code plugin that installs Char embeddable AI agent, configures WebMCP servers, [registers tools](https://github.com/webmachinelearning/webmcp/issues/15), and provides `/char:setup` wizard.

## Bridges & Adapters

- [nathan-gage/webmcp-bridge](https://github.com/nathan-gage/webmcp-bridge) - CLI + Chrome extension that bridges [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) tools to any MCP client (Claude, Cursor, Windsurf) via local WebSocket. Includes plugin marketplace for injecting bridge scripts on sites without native WebMCP.
- [three-water666/WebMCP](https://github.com/three-water666/WebMCP) - Universal bridge connecting web AI (Gemini, ChatGPT, DeepSeek) to local VS Code via MCP. VS Code extension + browser extension + Electron app with auto port discovery.
- [Juggernaath/WebMCP](https://github.com/Juggernaath/WebMCP) - Browser automation platform exposing 14+ MCP tools (navigate, click, type, screenshot, fill forms, manage tabs). Includes task recording, scraping, and playback.
- [victorzxj/webmcp-adapter](https://github.com/victorzxj/webmcp-adapter) - Universal adapter exposing frontend page actions as WebMCP tools with auto-discovery, schema generation, and [Service Worker](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) governance.
- [6missedcalls/openclaw-webmcp-skill](https://github.com/6missedcalls/openclaw-webmcp-skill) - OpenClaw skill enabling AI agents to discover and invoke [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) tools without DOM scraping.

## CMS & Platform Integrations

- [wmcp.dev](https://www.wmcp.dev/) - WordPress plugin adding WebMCP [declarative](https://github.com/webmachinelearning/webmcp/pull/76) attributes ([`toolname`](https://github.com/webmachinelearning/webmcp/pull/76), [`tooldescription`](https://github.com/webmachinelearning/webmcp/pull/76)) to Contact Form 7, Gravity Forms, WPForms, and WooCommerce forms.
- [chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect) - WordPress plugin exposing WebMCP REST API. AI agents authenticate via JWT and invoke tools like `wordpress.searchPosts` and `wordpress.getPost`.
- [tuvit/webmcp](https://github.com/tuvit/webmcp) - Wix platform extension injecting WebMCP attributes into Wix Stores pages for AI agent access to e-commerce data.

## Demo Applications

- [Travel Demo](https://travel-demo.bandarra.me/) - Official Chrome team demo using `searchFlights` tool via [imperative API](https://webmachinelearning.github.io/webmcp). Part of the Chrome Early Preview docs.
- [Playground WebMCP](https://webmcp.sh/) - Interactive browser playground for connecting to MCP servers, running tools, and querying in-browser PostgreSQL. ([Source](https://github.com/WebMCP-org/webmcp-sh)).
- [grzetich/webmcp-kanban](https://github.com/grzetich/webmcp-kanban) - React kanban board with 8 AI-callable tools (create, move, update, delete cards), drag-and-drop, and localStorage persistence. [Live demo](https://webmcp-kanban-demo.vercel.app).
- [WebMCP-org/big-calendar](https://github.com/WebMCP-org/big-calendar) - Next.js calendar with 15 WebMCP tools for CRUD, navigation, and settings. Includes embedded AI agent via chat widget.
- [WebMCP-org/ai-tinkerers-webmcp-demo](https://github.com/WebMCP-org/ai-tinkerers-webmcp-demo) - In-browser RAG pipeline with document ingestion, Transformers.js embeddings, Dexie/IndexedDB storage, and semantic search via WebMCP tools.
- [Legit-Control/webMCP-exploration](https://github.com/Legit-Control/webMCP-exploration) - React app integrating WebMCP with Legit SDK for Git-like versioned state mutations. AI agents edit calendar in isolated branches with visual previews.
- [SrinivasanTarget/WebMCP-demo](https://github.com/SrinivasanTarget/WebMCP-demo) - Full WebMCP polyfill demo with 15+ sample tools (math, DOM, storage, network) and an AI-agent simulator with JSON-RPC messaging.
- [tsunoyu/webmcp-travel-insurance](https://github.com/tsunoyu/webmcp-travel-insurance) - Travel insurance app demonstrating complete policy lifecycle (quoting, purchasing, claims) via [`navigator.modelContext`](https://webmachinelearning.github.io/webmcp) tools.
- [Cycasio/medsyn-web](https://github.com/Cycasio/medsyn-web) - Living evidence synthesis database for medical research with WebMCP tools for searching RCT studies, retrieving GRADE evidence summaries, and submitting studies for review.
- [khushalsagar/webmcp-demo](https://github.com/khushalsagar/webmcp-demo) - By the Chrome spec co-author ([@khushalsagar](https://github.com/khushalsagar)). Flight booking with both [declarative HTML](https://github.com/webmachinelearning/webmcp/pull/76) and [imperative JS](https://webmachinelearning.github.io/webmcp) approaches.
- [Doriandarko/webmcp-starter](https://github.com/Doriandarko/webmcp-starter) - Single-file "Midnight Eats" DoorDash-style demo with 9 tools (8 [imperative](https://webmachinelearning.github.io/webmcp), 1 [declarative](https://github.com/webmachinelearning/webmcp/pull/76)) and zero dependencies.
- [alecron/webmcp-demo](https://github.com/alecron/webmcp-demo) - Notes app with 5 tools (add, list, search, delete, stats) using [`navigator.modelContext.provideContext()`](https://webmachinelearning.github.io/webmcp).
- [alanw707/webmcp-readiness-radar](https://github.com/alanw707/webmcp-readiness-radar) - Web app scoring a site's WebMCP agent-readiness with pillar-wise breakdown and recommendations.

## Starter Templates & Courses

- [CodelyTV/webmcp-course](https://github.com/CodelyTV/webmcp-course) - Course examples for both [declarative](https://github.com/webmachinelearning/webmcp/pull/76) (`toolname`/`tooldescription` attributes) and [imperative](https://webmachinelearning.github.io/webmcp) (`navigator.modelContext`) APIs. Tied to [Codely Pro Spanish course](https://pro.codely.com/library/webmcp-anade-capacidades-agenticas-a-tu-web).
- [taqm/webmcp-sample](https://github.com/taqm/webmcp-sample) - Next.js + TypeScript starter template with Bun runtime, Biome linting, and Git hooks.
- [opentiny/docs](https://github.com/opentiny/docs) - Unified VitePress documentation for the OpenTiny NEXT ecosystem covering `WebMcpClient`, `WebMcpServer`, MCP browser extensions, and related projects.

## Articles & Tutorials

### Overviews

- [Chrome's WebMCP Early Preview: The End of AI Agents Clicking Buttons](https://dev.to/axrisi/chromes-webmcp-early-preview-the-end-of-ai-agents-clicking-buttons-b6e) - Third-party walkthrough covering both [imperative](https://webmachinelearning.github.io/webmcp) and [declarative](https://github.com/webmachinelearning/webmcp/pull/76) APIs, events, CSS pseudo-classes, and best practices.
- [What is WebMCP? Your Website Just Became a Function Call](https://www.nohackspod.com/blog/what-is-webmcp) - Explainer comparing WebMCP vs server-side approaches, covering [declarative HTML](https://github.com/webmachinelearning/webmcp/pull/76).
- [WebMCP (Dejan AI)](https://dejan.ai/blog/webmcp/) - SEO-focused analysis of tool descriptions as the next indexing problem.
- [WebMCP: Making the Web AI-Agent Ready](https://dev.to/sunny7899/webmcp-making-the-web-ai-agent-ready-5152) - Introduction with code samples for `registerTool`, [`provideContext`](https://webmachinelearning.github.io/webmcp), and [declarative forms](https://github.com/webmachinelearning/webmcp/pull/76).

### Technical Deep-Dives

- [Chrome's WebMCP Makes AI Agents Stop Pretending](https://medium.com/reading-sh/chromes-webmcp-makes-ai-agents-stop-pretending-e8c7da1ba650) - Trust model and security analysis.
- [Google Chrome Ships WebMCP (VentureBeat)](https://venturebeat.com/infrastructure/google-chrome-ships-webmcp-in-early-preview-turning-every-website-into-a) - Industry coverage with Google/Microsoft engineer quotes.
- [Google Previews WebMCP (Search Engine Land)](https://searchengineland.com/google-releases-preview-of-webmcp-how-ai-agents-interact-with-websites-469024) - Coverage exploring whether WebMCP is the next frontier of technical SEO.
- [Google's WebMCP Moves the Web Closer to a Structured Database (The Decoder)](https://the-decoder.com/googles-webmcp-moves-the-web-closer-to-becoming-a-structured-database-for-ai-agents/) - Broader web architecture implications.
- [How WebMCP Lets Developers Control AI Agents With JavaScript (The New Stack)](https://thenewstack.io/how-webmcp-lets-developers-control-ai-agents-with-javascript/) - Developer-focused technical analysis.
- [The WebMCP Revolution: Transforming SEO (xugj520)](https://www.xugj520.cn/en/archives/webmcp-seo-agents-capability-indexing.html) - How WebMCP shifts SEO from content to capability indexing.

### Interviews

- [WebMCP: Making Every Website a Tool — Alex Nahas Interview (Arcade.dev)](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview) - Amazon engineer ([@MiguelsPizza](https://github.com/MiguelsPizza)) who built MCP-B, inspiring the [W3C proposal](https://github.com/webmachinelearning/webmcp/pull/26).

### Quick Starts

- [WebMCP Just Landed in Chrome 146 (bug0.com)](https://bug0.com/blog/webmcp-chrome-146-guide) - Concise setup tutorial.
- [I Added 3 WebMCP Attributes (Medium)](https://medium.com/@harshiljani2002/i-have-added-3-webmcp-attributes-to-a-form-and-ai-agents-started-using-my-website-like-an-api-175231179545) - [Declarative](https://github.com/webmachinelearning/webmcp/pull/76) approach walkthrough.

### Agentic Web Context

- [MCP, A2A, NLWeb, and AGENTS.md (No Hacks Pod)](https://www.nohackspod.com/blog/agentic-web-protocols) - Where WebMCP fits among agentic protocols.
- [The Agentic Web (The New Stack)](https://thenewstack.io/the-agentic-web-how-ai-agents-are-shaping-the-webs-future/) - W3C perspective including WebMCP.
- [Inside the Agentic Web (All Things Open)](https://allthingsopen.org/articles/inside-the-agentic-web-what-developers-should-know) - Developer standards landscape overview.

## Videos & Talks

- [Don't Let AI Agents Push Your Buttons — Use WebMCP Instead!](https://www.youtube.com/watch?v=p1l8nkQAoUw) - [Khushal Sagar](https://github.com/khushalsagar) (Chrome Staff SWE, spec co-author), Nov 2025.
- [The Rise of WebMCP](https://www.youtube.com/watch?v=35oWt7u2b-g) - Sam Witteveen covering launch and web dev impact.

## Security Resources

### Official Threat Model

The [Security and Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) covers ([PR #55](https://github.com/webmachinelearning/webmcp/pull/55), [PR #59](https://github.com/webmachinelearning/webmcp/pull/59)):

1. **[Prompt Injection](https://github.com/webmachinelearning/webmcp/issues/45)** — Tool poisoning (malicious descriptions), output injection (tainted return values), tool-as-target (exploiting high-value tools).
2. **[Misrepresentation](https://github.com/webmachinelearning/webmcp/issues/53)** — Tools whose behavior doesn't match their description.
3. **Privacy Leakage** — Over-parameterized tools extracting unnecessary personal data.

### External Security Guides

- [MCP-B Security Best Practices](https://docs.mcp-b.ai/security) - Practical recommendations for WebMCP implementations.
- [Known Security Issues (MiguelsPizza Wiki)](https://github.com/MiguelsPizza/WebMCP/wiki/Known-Security-Issues-With-WebMCP) - Community-maintained attack vector list.
- [Protecting Against Indirect Injection (Microsoft)](https://developer.microsoft.com/blog/protecting-against-indirect-injection-attacks-mcp) - Applicable to WebMCP tool descriptions.
- [Mitigating Prompt Injections in Browser Use (Anthropic)](https://www.anthropic.com/research/prompt-injection-defenses) - Defense strategies for browser-based AI agents.

### Developer Checklist

- Validate all input in `execute()` — don't rely on schema alone.
- Use [`agent.requestUserInteraction()`](https://github.com/webmachinelearning/webmcp/issues/21) for destructive/financial actions.
- Check [`event.agentInvoked`](https://github.com/webmachinelearning/webmcp/issues/62) to differentiate agent vs human.
- Set [`readOnlyHint: true`](https://github.com/webmachinelearning/webmcp/issues/53) on read-only tools.
- Never return raw PII in tool responses ([privacy leakage](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#privacy-leakage)).
- Implement rate limiting in tool handlers.
- Keep descriptions positive ("Search flights") not negative ("Don't use for hotels").
- Log all tool invocations for audit.
- Update UI after execution — agents verify state via DOM.
- Use [`toolautosubmit`](https://github.com/webmachinelearning/webmcp/pull/76) only for idempotent operations.

## Community

- [W3C Web Machine Learning CG](https://www.w3.org/community/webmachinelearning/) - The W3C Community Group developing the WebMCP specification ([charter](https://webmachinelearning.github.io/charter/)). Open for anyone to join.
- [CG Meeting Minutes](https://github.com/webmachinelearning/meetings/tree/main/telcons) - Bi-weekly telecon minutes since September 2025, including all formal resolutions.
- [TPAC 2025 Session](https://github.com/webmachinelearning/webmcp/issues/35) - Full-day face-to-face session in [Kobe, Japan](https://www.w3.org/2025/11/TPAC/) (Nov 11, 2025) with TAG review and key resolutions.
- [Chrome AI Dev Preview Group](https://groups.google.com/a/chromium.org/g/chrome-ai-dev-preview-discuss/) - Official Google discussion group for Chrome AI features including WebMCP.
- [GitHub Issues](https://github.com/webmachinelearning/webmcp/issues) - Primary venue for async technical discussion and spec feedback.
- [Bug Reports](https://crbug.com/new?component=2021259) - Chrome implementation bug reports.
- [Automation: webmcp-monthly-roundup](https://github.com/joellobo1234/webmcp-monthly-roundup-gh-action) - GitHub Action generating monthly newsletter roundups of WebMCP repo activity.
- [r/HowToAIAgent](https://www.reddit.com/r/HowToAIAgent/comments/1r36bec/webmcp_just_dropped_in_chrome_146_and_now_your/) - Chrome 146 launch discussion.
- [r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1r2m8cw/chromes_webmcp_makes_ai_agents_stop_pretending/) - Developer reactions.
- [WebMCP List](https://webmcplist.com) - Open directory of WebMCP-enabled websites with search, category browsing, and community submissions. Itself WebMCP-enabled with 4 tools for AI agent discovery.

---

## Specification

### Browser Support

| Browser | Status | Version | How to Enable |
|---|---|---|---|
| **Chrome** | Early Preview | 146.0.7672.0+ (Canary) | `chrome://flags` → "WebMCP for testing" |
| **Edge** | Expected | TBD | Same Chromium flag |
| **Firefox** | Not yet | — | — |
| **Safari** | Not yet | — | — |

```javascript
if ("modelContext" in navigator) {
  // WebMCP is supported
}
```

### Spec Documents

| Document | Link | Description |
|---|---|---|
| **Normative Spec** | [webmachinelearning.github.io/webmcp](https://webmachinelearning.github.io/webmcp) | Full spec: [`navigator.modelContext`](https://webmachinelearning.github.io/webmcp#navigator.modelContext), [`registerTool()`](https://webmachinelearning.github.io/webmcp#registerTool), [`provideContext()`](https://webmachinelearning.github.io/webmcp#provideContext), events, CSS pseudo-classes |
| **Source Repo** | [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) | Bikeshed spec, explainers, [security/privacy docs](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md), issue tracker |
| **Chrome Early Preview** | [developer.chrome.com](https://developer.chrome.com/blog/webmcp-epp) | Setup instructions, API overview, demo walkthrough |
| **Declarative API Explainer** | [PR #76](https://github.com/webmachinelearning/webmcp/pull/76) | HTML form-based tools: [`toolname`](https://github.com/webmachinelearning/webmcp/pull/76), [`tooldescription`](https://github.com/webmachinelearning/webmcp/pull/76), [`toolautosubmit`](https://github.com/webmachinelearning/webmcp/pull/76) |
| **Service Workers** | [service-workers.md](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) | Background tool execution without a visible tab |
| **Security & Privacy** | [security-privacy.md](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) | [Prompt injection](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#prompt-injection), [misrepresentation](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#misrepresentation), [over-parameterization](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#privacy-leakage) |
| **Deep Discussion Analysis** | [docs/spec-discussions/](docs/spec-discussions/) | All 55 issues + 20 PRs analyzed by theme |

### Key Spec Issues

| Topic | Issue | Author | Status |
|---|---|---|---|
| Accessibility via agentic interfaces | [#65](https://github.com/webmachinelearning/webmcp/issues/65) | [@anssiko](https://github.com/anssiko) | Open |
| In-page agent API (`executeTool` vs `createClient`) | [#51](https://github.com/webmachinelearning/webmcp/issues/51) | [@khushalsagar](https://github.com/khushalsagar) | Open |
| Elicitation / `requestUserInteraction` | [#21](https://github.com/webmachinelearning/webmcp/issues/21) | [@bwalderman](https://github.com/bwalderman) | Resolved |
| Tool annotations & side-effect hints | [#53](https://github.com/webmachinelearning/webmcp/issues/53) | [@victorhuangwq](https://github.com/victorhuangwq) | Open |
| Agent identity | [#54](https://github.com/webmachinelearning/webmcp/issues/54) | [@EmLauber](https://github.com/EmLauber) | Deferred |
| WebExtensions API | [#74](https://github.com/webmachinelearning/webmcp/issues/74) | [@reillyeon](https://github.com/reillyeon) | Open |
| Iframe tool registration | [#57](https://github.com/webmachinelearning/webmcp/issues/57) | [@khushalsagar](https://github.com/khushalsagar) | Open |
| User gesture activation | [#62](https://github.com/webmachinelearning/webmcp/issues/62) | [@beaufortfrancois](https://github.com/beaufortfrancois) | Open |
| File attachments | [#81](https://github.com/webmachinelearning/webmcp/issues/81) | [@markafoltz](https://github.com/markafoltz) | Open |
| Streaming arguments | [#82](https://github.com/webmachinelearning/webmcp/issues/82) | [@MiguelsPizza](https://github.com/MiguelsPizza) | Open |
| Scope clarification | [#43](https://github.com/webmachinelearning/webmcp/issues/43) | — | Open |

### Key Resolutions

Formal decisions from W3C CG meetings ([minutes archive](https://github.com/webmachinelearning/meetings)):

| Date | Resolution | Issue |
|---|---|---|
| Sep 2025 | **Human-in-the-loop first.** Automation deferred until security model matures. | [#27](https://github.com/webmachinelearning/webmcp/issues/27) |
| Sep 2025 | **SDK abstraction.** Browser translates between page and MCP client; decoupled from MCP version. | [#32](https://github.com/webmachinelearning/webmcp/issues/32) |
| Oct 2025 | **`navigator.modelContext`** is the root object name. | [#24](https://github.com/webmachinelearning/webmcp/issues/24) |
| Oct 2025 | **Tools as discovery.** Declarative API enables crawling/indexing without JS. | [#8](https://github.com/webmachinelearning/webmcp/issues/8) |
| Oct 2025 | **Both [`provideContext()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-providecontext) and [`registerTool()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-registertool)** supported. | [#15](https://github.com/webmachinelearning/webmcp/issues/15) |
| Oct 2025 | **No JS injection** by external agents. Extension API / CDP preferred. | [#16](https://github.com/webmachinelearning/webmcp/issues/16) |
| Oct 2025 | **[`requestUserInteraction()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-requestuserinteraction)** for elicitation. Block abusive sites permanently. | [#21](https://github.com/webmachinelearning/webmcp/issues/21) |
| Oct 2025 | **No user-takeover notification** needed. Elicitation handles it. | [#20](https://github.com/webmachinelearning/webmcp/issues/20) |
| Nov 2025 | **TAG endorsement.** Continue as high-level API. Coordinate with AI Agent Protocol CG. | [#35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Nov 2025 | **Cross-origin scoped out of v1.** Same-origin tool registration only. | [#52](https://github.com/webmachinelearning/webmcp/issues/52) |
| Nov 2025 | **Agent identity deferred.** No clear v1 need. | [#54](https://github.com/webmachinelearning/webmcp/issues/54) |
| Jan 2026 | **Declarative complements imperative.** HTML form attributes + JS API. | [PR #26](https://github.com/webmachinelearning/webmcp/pull/26) |
| Jan 2026 | **[`AbortSignal`](https://webmachinelearning.github.io/webmcp/#dom-executetoolparams) for cancellation.** Tool callbacks receive abort signal. | [#48](https://github.com/webmachinelearning/webmcp/issues/48) |
| Jan 2026 | **Concurrent execution punted.** Depends on in-page agent API design. | [#47](https://github.com/webmachinelearning/webmcp/issues/47) |
| Feb 2026 | **[`outputSchema`](https://webmachinelearning.github.io/webmcp/#dom-toolregistration-outputschema) added.** Structured output definitions for tools. | [#9](https://github.com/webmachinelearning/webmcp/issues/9) |

## API Quick Reference

### [Imperative API](https://webmachinelearning.github.io/webmcp)

```javascript
// Register a tool (#15)
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
  annotations: { readOnlyHint: true },  // #53
  execute: async ({ origin, destination, date }) => {
    const results = await searchFlightsAPI(origin, destination, date);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});

// Replace all tools (#15)
navigator.modelContext.provideContext({ tools: [/* ... */] });

// Remove tools
navigator.modelContext.unregisterTool("searchFlights");
navigator.modelContext.clearContext();
```

### [Declarative API](https://github.com/webmachinelearning/webmcp/pull/76)

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

### [Events & CSS](https://webmachinelearning.github.io/webmcp)

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

### [User Interaction](https://github.com/webmachinelearning/webmcp/issues/21)

```javascript
execute: async ({ productId }, agent) => {
  const ok = await agent.requestUserInteraction(async () => {
    return confirm(`Purchase ${productId}?`);
  });
  if (!ok) throw new Error("Canceled");
  return { content: [{ type: "text", text: "Purchased" }] };
}
```

## Key People

| Person | Affiliation | Role | Links |
|---|---|---|---|
| **Anssi Kostiainen** | W3C / Intel | CG Chair, coordinates with TAG and AAIF ([#35](https://github.com/webmachinelearning/webmcp/issues/35)), spec draft ([PR #64](https://github.com/webmachinelearning/webmcp/pull/64)), WebIDL ([PR #75](https://github.com/webmachinelearning/webmcp/pull/75)) | [GitHub](https://github.com/anssiko) |
| **Brandon Walderman** | Microsoft | Spec co-author, initial explainer ([PR #1](https://github.com/webmachinelearning/webmcp/pull/1)), service workers ([PR #4](https://github.com/webmachinelearning/webmcp/pull/4)), elicitation ([#21](https://github.com/webmachinelearning/webmcp/issues/21)), naming ([#24](https://github.com/webmachinelearning/webmcp/issues/24)), API design ([#15](https://github.com/webmachinelearning/webmcp/issues/15)) | [GitHub](https://github.com/bwalderman) · [LinkedIn](https://www.linkedin.com/in/brandonwalderman) |
| **David Bokan** | Google | Spec co-author, discovery ([#8](https://github.com/webmachinelearning/webmcp/issues/8)), output schema ([#9](https://github.com/webmachinelearning/webmcp/issues/9)), external agents ([#16](https://github.com/webmachinelearning/webmcp/issues/16)) | [GitHub](https://github.com/bokand) |
| **Khushal Sagar** | Google | Spec co-author, Chrome lead, SDK ([#32](https://github.com/webmachinelearning/webmcp/issues/32)), `createClient` ([#51](https://github.com/webmachinelearning/webmcp/issues/51)), iframes ([#57](https://github.com/webmachinelearning/webmcp/issues/57)), annotations ([#53](https://github.com/webmachinelearning/webmcp/issues/53)), scope ([#52](https://github.com/webmachinelearning/webmcp/issues/52)), concurrency ([#47](https://github.com/webmachinelearning/webmcp/issues/47), [#49](https://github.com/webmachinelearning/webmcp/issues/49)) | [GitHub](https://github.com/khushalsagar) · [LinkedIn](https://www.linkedin.com/in/khushal-sagar) |
| **Dominic Farolino** | Google | Spec editor, declarative explainer ([PR #76](https://github.com/webmachinelearning/webmcp/pull/76)), iframe policy ([#57](https://github.com/webmachinelearning/webmcp/issues/57)) | [GitHub](https://github.com/domfarolino) · [LinkedIn](https://www.linkedin.com/in/domfarolino) · [X](https://x.com/domfarolino) |
| **Victor Huang** | Google | Security & Privacy ([PR #55](https://github.com/webmachinelearning/webmcp/pull/55), [PR #59](https://github.com/webmachinelearning/webmcp/pull/59)), threat model ([#45](https://github.com/webmachinelearning/webmcp/issues/45)), annotations ([#53](https://github.com/webmachinelearning/webmcp/issues/53)), identity ([#54](https://github.com/webmachinelearning/webmcp/issues/54)) | [GitHub](https://github.com/victorhuangwq) |
| **Mark Foltz** | Google | Spec editor, file attachments ([#81](https://github.com/webmachinelearning/webmcp/issues/81)), streaming ([#82](https://github.com/webmachinelearning/webmcp/issues/82)), build system ([PR #77](https://github.com/webmachinelearning/webmcp/pull/77)–[#80](https://github.com/webmachinelearning/webmcp/pull/80)) | [GitHub](https://github.com/markafoltz) |
| **Reilly Grant** | Google | WebExtensions API ([#74](https://github.com/webmachinelearning/webmcp/issues/74)), `navigator` placement ([#24](https://github.com/webmachinelearning/webmcp/issues/24)), component coordination ([#15](https://github.com/webmachinelearning/webmcp/issues/15)) | [GitHub](https://github.com/reillyeon) |
| **Anders Ruud** | Google | Declarative deep dive — forms, radio buttons, labels ([#66](https://github.com/webmachinelearning/webmcp/issues/66)–[#69](https://github.com/webmachinelearning/webmcp/issues/69), [#71](https://github.com/webmachinelearning/webmcp/issues/71)) | [GitHub](https://github.com/andruud) |
| **Francois Beaufort** | Google | Chrome Early Preview, user gesture activation ([#62](https://github.com/webmachinelearning/webmcp/issues/62)), `modelContextTesting` ([#74](https://github.com/webmachinelearning/webmcp/issues/74)), JS examples ([PR #61](https://github.com/webmachinelearning/webmcp/pull/61)) | [GitHub](https://github.com/beaufortfrancois) |
| **Andre Bandarra** | Google | Chrome Early Preview, [travel demo](https://travel-demo.bandarra.me/) | [GitHub](https://github.com/andreban) · [X](https://x.com/andreban) · [Blog](https://bandarra.me/) |
| **Alex Nahas** | Amazon (prev.) | Original WebMCP concept, [MCP-B](https://mcp-b.ai) creator, declarative proposal ([PR #26](https://github.com/webmachinelearning/webmcp/pull/26)), async registration ([#30](https://github.com/webmachinelearning/webmcp/issues/30)) | [GitHub](https://github.com/MiguelsPizza) · [LinkedIn](https://www.linkedin.com/in/alex-nahas) |
| **Jason McGhee** | Independent | Early WebMCP implementation, external MCP client API ([#23](https://github.com/webmachinelearning/webmcp/issues/23)) | [GitHub](https://github.com/jasonjmcghee) · [X](https://x.com/_jason_today) |
| **Ilya Grigorik** | Shopify | Commerce perspective, agent-to-agent ([#31](https://github.com/webmachinelearning/webmcp/issues/31)) | [GitHub](https://github.com/igrigorik) |
| **Leonie Watson** | TPGi | Accessibility expert, screen readers ([#65](https://github.com/webmachinelearning/webmcp/issues/65)) | [GitHub](https://github.com/LJWatson) |

## Contributing

See [contributing.md](contributing.md) for guidelines. Only submit resources **directly about the WebMCP browser standard** (`navigator.modelContext` API or `toolname`/`tooldescription` HTML attributes). Do not submit general server-side MCP resources. (Follow [awesome-list standards](https://github.com/sindresorhus/awesome) for entry quality.)

## Maintainers

- Yigit Konur [@yigitkonur](https://x.com/yigitkonur)
- Web-MCP.net Crew [@web-mcp.net](https://web-mcp.net)

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0)

To the extent possible under law, the maintainers have waived all copyright and related or neighboring rights to this work.
