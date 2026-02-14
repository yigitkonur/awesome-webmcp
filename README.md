# Awesome WebMCP

> A curated list of resources for WebMCP — the W3C web standard that lets websites expose structured tools to AI agents via `navigator.modelContext`.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Chrome 146+](https://img.shields.io/badge/Chrome-146+-blue.svg)](https://developer.chrome.com/blog/webmcp-epp)
[![W3C Draft](https://img.shields.io/badge/W3C-Community%20Draft-green.svg)](https://webmachinelearning.github.io/webmcp)

## Contents

- [Specification & Proposals](#specification--proposals)
- [Specification Status](#specification-status)
- [Browser Support](#browser-support)
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
- [Automation](#automation)
- [Community](#community)
- [API Quick Reference](#api-quick-reference)
- [Key People](#key-people)

## Specification & Proposals

- [W3C WebMCP Specification](https://webmachinelearning.github.io/webmcp) - Normative spec draft defining [`navigator.modelContext`](https://webmachinelearning.github.io/webmcp#navigator.modelContext), [`registerTool()`](https://webmachinelearning.github.io/webmcp#registerTool), [`provideContext()`](https://webmachinelearning.github.io/webmcp#provideContext), [declarative HTML form attributes](https://github.com/webmachinelearning/webmcp/pull/76), [`toolactivated` and `toolcancel` events](https://webmachinelearning.github.io/webmcp#events), and [`:tool-form-active` and `:tool-submit-active` CSS pseudo-classes](https://webmachinelearning.github.io/webmcp#css-pseudo-classes).
- [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) - Official W3C Community Group repo with Bikeshed spec source, explainers, [security/privacy docs](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md), and issue tracker.
- [spiralgang/Workspace_Sync_Target](https://github.com/spiralgang/Workspace_Sync_Target) - W3C Web Machine Learning CG repository exploring WebMCP and GitHub-native agent orchestration patterns.
- [Chrome WebMCP Early Preview](https://developer.chrome.com/blog/webmcp-epp) - Chrome for Developers blog with setup instructions, API overview, and demo walkthrough.
- [Service Workers Explainer](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) - Background tool execution without a visible tab via JIT installation, session management, and discovery.
- [Declarative API Explainer (PR #76)](https://github.com/webmachinelearning/webmcp/pull/76) - Formal explainer for HTML form-based tool registration using [`toolname`](https://github.com/webmachinelearning/webmcp/pull/76), [`tooldescription`](https://github.com/webmachinelearning/webmcp/pull/76), and [`toolautosubmit`](https://github.com/webmachinelearning/webmcp/pull/76) attributes.
- [Security & Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) - Official threat model covering [prompt injection](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#prompt-injection), [misrepresentation](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#misrepresentation), and [over-parameterization](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#privacy-leakage).
- [Accessibility Considerations (Issue #65)](https://github.com/webmachinelearning/webmcp/issues/65) - WebMCP's potential to improve accessibility through agentic interfaces.
- [In-Page Agent API (Issue #51)](https://github.com/webmachinelearning/webmcp/issues/51) - Defining API for agents embedded in the page to consume declared tools.
- [Elicitation / requestUserInteraction (Issue #21)](https://github.com/webmachinelearning/webmcp/issues/21) - Design for yielding control to the user during tool execution, with abuse prevention.
- [Tool Annotations & Side-Effect Hints (Issue #53)](https://github.com/webmachinelearning/webmcp/issues/53) - Structured metadata for tool safety hints like [`readOnlyHint`](https://github.com/webmachinelearning/webmcp/issues/53) and [`destructiveHint`](https://github.com/webmachinelearning/webmcp/issues/53).
- [Agent Identity (Issue #54)](https://github.com/webmachinelearning/webmcp/issues/54) - Discussion on whether tools should know which agent is invoking them.
- [Scope Clarification (Issue #43)](https://github.com/webmachinelearning/webmcp/issues/43) - What WebMCP covers vs. what belongs to the agent layer.
- [Spec Discussion Analysis](docs/spec-discussions/) - Deep analysis of all 55 issues and 20 PRs, organized by theme. Covers [security debates](docs/spec-discussions/), API naming history, [declarative proposal evolution](https://github.com/webmachinelearning/webmcp/pull/76), and community feedback.

## Specification Status

### Key Resolutions

Formal decisions from W3C CG meetings ([minutes archive](https://github.com/webmachinelearning/meetings)):

- **Human-in-the-loop first.** Automation support deferred until security model matures. ([#27](https://github.com/webmachinelearning/webmcp/issues/27) by [@khushalsagar](https://github.com/khushalsagar))
- **SDK abstraction.** Browser provides a translation layer between page and MCP client; decoupled from MCP version. ([#32](https://github.com/webmachinelearning/webmcp/issues/32) by [@khushalsagar](https://github.com/khushalsagar))
- **`navigator.modelContext`** is the root object name. ([#24](https://github.com/webmachinelearning/webmcp/issues/24) by [@bwalderman](https://github.com/bwalderman))
- **Tools as discovery mechanism.** Declarative API explored to enable crawling and indexing without executing JS. ([#8](https://github.com/webmachinelearning/webmcp/issues/8) by [@bokand](https://github.com/bokand))
- **Both [`provideContext()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-providecontext) and [`registerTool()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-registertool)/`unregisterTool()` supported.** Batch replacement for SPAs, incremental for multi-component pages. ([#15](https://github.com/webmachinelearning/webmcp/issues/15) by [@bwalderman](https://github.com/bwalderman))
- **No JS injection by external agents.** Higher-level hooks (extension API, CDP) preferred. ([#16](https://github.com/webmachinelearning/webmcp/issues/16) by [@bokand](https://github.com/bokand))
- **[`requestUserInteraction()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-requestuserinteraction) for elicitation.** Abusive sites can be permanently blocked; error thrown so legitimate sites implement fallback. ([#21](https://github.com/webmachinelearning/webmcp/issues/21) by [@bwalderman](https://github.com/bwalderman))
- **No concrete use case for user-takeover notification.** Elicitation ([#21](https://github.com/webmachinelearning/webmcp/issues/21)) handles site-initiated handoff. ([#20](https://github.com/webmachinelearning/webmcp/issues/20) by [@khushalsagar](https://github.com/khushalsagar))
- **TAG endorsement.** Continue as high-level API; coordinate with AI Agent Protocol CG on new protocols. MCP governance moved to Linux Foundation's AAIF. ([#35](https://github.com/webmachinelearning/webmcp/issues/35) by [@xiaochengh](https://github.com/xiaochengh))
- **Cross-origin agents scoped out of v1.** Focus on same-origin tool registration initially. ([#52](https://github.com/webmachinelearning/webmcp/issues/52) by [@khushalsagar](https://github.com/khushalsagar))
- **Agent identity: revisit later.** No clear need for browser-mediated agent identity in v1. ([#54](https://github.com/webmachinelearning/webmcp/issues/54) by [@EmLauber](https://github.com/EmLauber))
- **Declarative API complements imperative.** HTML form attributes (`toolname`, `tooldescription`) work alongside JS API. Chrome began prototyping. ([PR #26](https://github.com/webmachinelearning/webmcp/pull/26) by [@MiguelsPizza](https://github.com/MiguelsPizza))
- **[`AbortSignal`](https://webmachinelearning.github.io/webmcp/#dom-executetoolparams) for cancellation.** Tool execute callbacks receive a signal for clean abort. ([#48](https://github.com/webmachinelearning/webmcp/issues/48) by [@khushalsagar](https://github.com/khushalsagar))
- **Concurrent tool execution punted.** Depends on in-page agent API ([#51](https://github.com/webmachinelearning/webmcp/issues/51)) design. ([#47](https://github.com/webmachinelearning/webmcp/issues/47) by [@khushalsagar](https://github.com/khushalsagar))
- **[`outputSchema`](https://webmachinelearning.github.io/webmcp/#dom-toolregistration-outputschema) added to imperative API.** Structured output definitions for tools. ([#9](https://github.com/webmachinelearning/webmcp/issues/9) by [@bokand](https://github.com/bokand))

### Open Design Questions

Active debates with no resolution yet:

- **In-page agent execution API** — Should pages be able to call their own tools? `executeTool()` vs `createClient()`. ([#51](https://github.com/webmachinelearning/webmcp/issues/51) by [@khushalsagar](https://github.com/khushalsagar))
- **WebExtensions API** — Dedicated extension API for tool enumeration/invocation without content script injection. ([#74](https://github.com/webmachinelearning/webmcp/issues/74) by [@reillyeon](https://github.com/reillyeon))
- **Iframe tool registration** — Permission Policy vs Document Policy for cross-origin tool delegation. ([#57](https://github.com/webmachinelearning/webmcp/issues/57) by [@khushalsagar](https://github.com/khushalsagar))
- **Declarative form mapping** — Radio buttons, multiple labels, date min/max constraints in JSON Schema. ([#63](https://github.com/webmachinelearning/webmcp/issues/63) by [@khushalsagar](https://github.com/khushalsagar), [#66](https://github.com/webmachinelearning/webmcp/issues/66), [#67](https://github.com/webmachinelearning/webmcp/issues/67), [#68](https://github.com/webmachinelearning/webmcp/issues/68), [#69](https://github.com/webmachinelearning/webmcp/issues/69) by [@andruud](https://github.com/andruud), [#71](https://github.com/webmachinelearning/webmcp/issues/71) by [@andruud](https://github.com/andruud))
- **User gesture activation** — Should agent-invoked tool calls receive transient user activation? ([#62](https://github.com/webmachinelearning/webmcp/issues/62) by [@beaufortfrancois](https://github.com/beaufortfrancois))
- **Concurrent multi-agent execution** — Multiple agents on same page; browser agent and extension agents interacting simultaneously. ([#49](https://github.com/webmachinelearning/webmcp/issues/49) by [@khushalsagar](https://github.com/khushalsagar))
- **File attachments** — Supporting file uploads via imperative API. ([#81](https://github.com/webmachinelearning/webmcp/issues/81) by [@markafoltz](https://github.com/markafoltz))
- **Streaming arguments** — Progressive delivery of tool arguments to execute callbacks. ([#82](https://github.com/webmachinelearning/webmcp/issues/82) by [@MiguelsPizza](https://github.com/MiguelsPizza))

## Browser Support

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
- [wodiddfox/webmcp-excellent-core](https://github.com/wodiddfox/webmcp-excellent-core) - Production-grade WebMCP skill spec with strict scope boundaries, multi-tier confirmation gates, schema validation, deterministic output tracing, and structured fallback codes. [npm](https://www.npmjs.com/package/webmcp-excellent-core).

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
- [WispAyr/webmcp-integration](https://github.com/WispAyr/webmcp-integration) - Research and architecture for integrating WebMCP into the Clawdbot browser automation agent with 6-phase implementation roadmap and tool capability registry.
- [shepherdvovkes/webmcp](https://github.com/shepherdvovkes/webmcp) - MCP server for the Ukrainian court registry with document monitoring, structured data extraction, semantic search via pgvector, and RAG-based case analysis.

## Demo Applications

### Substantial Demos

- [Travel Demo](https://travel-demo.bandarra.me/) - Official Chrome team demo using `searchFlights` tool via [imperative API](https://webmachinelearning.github.io/webmcp/#imperative-api). Part of the Chrome Early Preview docs.
- [Playground WebMCP](https://webmcp.sh/) - Interactive browser playground for connecting to MCP servers, running tools, and querying in-browser PostgreSQL. ([Source](https://github.com/WebMCP-org/webmcp-sh)).
- [grzetich/webmcp-kanban](https://github.com/grzetich/webmcp-kanban) - React kanban board with 8 AI-callable tools (create, move, update, delete cards), drag-and-drop, and localStorage persistence. [Live demo](https://webmcp-kanban-demo.vercel.app).
- [WebMCP-org/big-calendar](https://github.com/WebMCP-org/big-calendar) - Next.js calendar with 15 WebMCP tools for CRUD, navigation, and settings. Includes embedded AI agent via chat widget.
- [WebMCP-org/ai-tinkerers-webmcp-demo](https://github.com/WebMCP-org/ai-tinkerers-webmcp-demo) - In-browser RAG pipeline with document ingestion, Transformers.js embeddings, Dexie/IndexedDB storage, and semantic search via WebMCP tools.
- [Legit-Control/webMCP-exploration](https://github.com/Legit-Control/webMCP-exploration) - React app integrating WebMCP with Legit SDK for Git-like versioned state mutations. AI agents edit calendar in isolated branches with visual previews.
- [SrinivasanTarget/WebMCP-demo](https://github.com/SrinivasanTarget/WebMCP-demo) - Full WebMCP polyfill demo with 15+ sample tools (math, DOM, storage, network) and an AI-agent simulator with JSON-RPC messaging.
- [tsunoyu/webmcp-travel-insurance](https://github.com/tsunoyu/webmcp-travel-insurance) - Travel insurance app demonstrating complete policy lifecycle (quoting, purchasing, claims) via [`navigator.modelContext`](https://webmachinelearning.github.io/webmcp/#modelcontext) tools.
- [Cycasio/medsyn-web](https://github.com/Cycasio/medsyn-web) - Living evidence synthesis database for medical research with WebMCP tools for searching RCT studies, retrieving GRADE evidence summaries, and submitting studies for review.
- [marisviana/webmcp_demo-site](https://github.com/marisviana/webmcp_demo-site) - E-commerce demo with WebMCP tools for product filtering and cart management.
- [ripulio/webmcp-tools](https://github.com/ripulio/webmcp-tools) - Catalog of production-ready WebMCP tool definitions with schemas, metadata, and build pipeline.

### Minimal Demos

- [khushalsagar/webmcp-demo](https://github.com/khushalsagar/webmcp-demo) - By the Chrome spec co-author ([@khushalsagar](https://github.com/khushalsagar)). Flight booking with both [declarative HTML](https://github.com/webmachinelearning/webmcp/pull/76) and [imperative JS](https://webmachinelearning.github.io/webmcp/#imperative-api) approaches.
- [Doriandarko/webmcp-starter](https://github.com/Doriandarko/webmcp-starter) - Single-file "Midnight Eats" DoorDash-style demo with 9 tools (8 [imperative](https://webmachinelearning.github.io/webmcp/#imperative-api), 1 [declarative](https://github.com/webmachinelearning/webmcp/pull/76)) and zero dependencies.
- [alecron/webmcp-demo](https://github.com/alecron/webmcp-demo) - Notes app with 5 tools (add, list, search, delete, stats) using [`navigator.modelContext.provideContext()`](https://webmachinelearning.github.io/webmcp/#modelcontext).
- [best-seller/webmcp-demo](https://github.com/best-seller/webmcp-demo) - [Imperative](https://webmachinelearning.github.io/webmcp/#imperative-api) + [declarative API](https://github.com/webmachinelearning/webmcp/pull/76) examples for AI agent interaction.
- [alanw707/webmcp-readiness-radar](https://github.com/alanw707/webmcp-readiness-radar) - Web app scoring a site's WebMCP agent-readiness with pillar-wise breakdown and recommendations.
- [pawn-4-git/webmcp_test](https://github.com/pawn-4-git/webmcp_test) - Product review extraction demo using a `getProductReviews` WebMCP tool.

## Starter Templates & Courses

- [CodelyTV/webmcp-course](https://github.com/CodelyTV/webmcp-course) - Course examples for both [declarative](https://github.com/webmachinelearning/webmcp/pull/76) (`toolname`/`tooldescription` attributes) and [imperative](https://webmachinelearning.github.io/webmcp/#imperative-api) (`navigator.modelContext`) APIs. Tied to [Codely Pro Spanish course](https://pro.codely.com/library/webmcp-anade-capacidades-agenticas-a-tu-web).
- [taqm/webmcp-sample](https://github.com/taqm/webmcp-sample) - Next.js + TypeScript starter template with Bun runtime, Biome linting, and Git hooks.
- [lemonhall/webmcp_readme](https://github.com/lemonhall/webmcp_readme) - Guide and explanation of WebMCP with practical React flight search demo.
- [opentiny/docs](https://github.com/opentiny/docs) - Unified VitePress documentation for the OpenTiny NEXT ecosystem covering `WebMcpClient`, `WebMcpServer`, MCP browser extensions, and related projects.

## Articles & Tutorials

### Overviews

- [Chrome's WebMCP Early Preview: The End of AI Agents Clicking Buttons](https://dev.to/axrisi/chromes-webmcp-early-preview-the-end-of-ai-agents-clicking-buttons-b6e) - Third-party walkthrough covering both [imperative](https://webmachinelearning.github.io/webmcp/#imperative-api) and [declarative](https://github.com/webmachinelearning/webmcp/pull/76) APIs, events, CSS pseudo-classes, and best practices.
- [What is WebMCP? Your Website Just Became a Function Call](https://www.nohackspod.com/blog/what-is-webmcp) - Explainer comparing WebMCP vs server-side approaches, covering [declarative HTML](https://github.com/webmachinelearning/webmcp/pull/76).
- [WebMCP (Dejan AI)](https://dejan.ai/blog/webmcp/) - SEO-focused analysis of tool descriptions as the next indexing problem.
- [WebMCP: Making the Web AI-Agent Ready](https://dev.to/sunny7899/webmcp-making-the-web-ai-agent-ready-5152) - Introduction with code samples for `registerTool`, [`provideContext`](https://webmachinelearning.github.io/webmcp/#modelcontext), and [declarative forms](https://github.com/webmachinelearning/webmcp/pull/76).

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

## Automation

- [joellobo1234/webmcp-monthly-roundup-gh-action](https://github.com/joellobo1234/webmcp-monthly-roundup-gh-action) - GitHub Action generating monthly newsletter roundups of WebMCP repo activity with auto-publish to GitHub Discussions and contributor recognition.
- [alanw707/daily-mvp-2026-02-13-webmcp-readiness-radar](https://github.com/alanw707/daily-mvp-2026-02-13-webmcp-readiness-radar) - Daily MVP iteration of the Readiness Radar with site-type-aware scoring (SaaS, e-commerce, media, marketplace).

## Community

- [W3C Web Machine Learning CG](https://www.w3.org/community/webmachinelearning/) - The W3C Community Group developing the WebMCP specification ([charter](https://webmachinelearning.github.io/charter/)). Open for anyone to join.
- [CG Meeting Minutes](https://github.com/webmachinelearning/meetings/tree/main/telcons) - Bi-weekly telecon minutes since September 2025, including all formal resolutions.
- [TPAC 2025 Session](https://github.com/webmachinelearning/webmcp/issues/35) - Full-day face-to-face session in [Kobe, Japan](https://www.w3.org/2025/11/TPAC/) (Nov 11, 2025) with TAG review and key resolutions.
- [Chrome AI Dev Preview Group](https://groups.google.com/a/chromium.org/g/chrome-ai-dev-preview-discuss/) - Official Google discussion group for Chrome AI features including WebMCP.
- [GitHub Issues](https://github.com/webmachinelearning/webmcp/issues) - Primary venue for async technical discussion and spec feedback.
- [Bug Reports](https://crbug.com/new?component=2021259) - Chrome implementation bug reports.
- [r/HowToAIAgent](https://www.reddit.com/r/HowToAIAgent/comments/1r36bec/webmcp_just_dropped_in_chrome_146_and_now_your/) - Chrome 146 launch discussion.
- [r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1r2m8cw/chromes_webmcp_makes_ai_agents_stop_pretending/) - Developer reactions.

## API Quick Reference

### [Imperative API](https://webmachinelearning.github.io/webmcp)

See the [spec](https://webmachinelearning.github.io/webmcp) for detailed Imperative API documentation.

```javascript
// Register a single tool - decided in #15 (https://github.com/webmachinelearning/webmcp/issues/15)
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
  // readOnlyHint annotations - see #53 (https://github.com/webmachinelearning/webmcp/issues/53)
  annotations: { readOnlyHint: true },
  execute: async ({ origin, destination, date }) => {
    const results = await searchFlightsAPI(origin, destination, date);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});

// Replace all tools - provideContext API from #15 (https://github.com/webmachinelearning/webmcp/issues/15)
navigator.modelContext.provideContext({ tools: [/* ... */] });

// Remove one tool / all tools - unregisterTool #15, clearContext per spec
navigator.modelContext.unregisterTool("searchFlights");
navigator.modelContext.clearContext();
```

### [Declarative API](https://github.com/webmachinelearning/webmcp/pull/76)

See [PR #76](https://github.com/webmachinelearning/webmcp/pull/76) for Declarative API Explainer.

```html
<!-- toolname, tooldescription, toolautosubmit attributes - PR #76 -->
<form toolname="book_table"
      tooldescription="Reserve a table at this restaurant"
      toolautosubmit action="/api/reserve">
  <!-- toolparamdescription attribute - PR #76 -->
  <input type="date" name="date" required toolparamdescription="Reservation date">
  <input type="time" name="time" required toolparamdescription="Reservation time">
  <input type="number" name="guests" min="1" max="20" required>
  <button type="submit">Reserve</button>
</form>
```

### [Events & CSS](https://webmachinelearning.github.io/webmcp)

See the [spec](https://webmachinelearning.github.io/webmcp) for events and CSS pseudo-classes.

```javascript
form.addEventListener("submit", (e) => {
  // agentInvoked property - defined in spec
  if (e.agentInvoked) {
    e.preventDefault();
    e.respondWith(handleAgentSubmission(e));
  }
});

window.addEventListener('toolactivated', ({ toolName }) => { /* ... */ });
window.addEventListener('toolcancel', ({ toolName }) => { /* ... */ });
```

```css
/* :tool-form-active and :tool-submit-active pseudo-classes - per spec */
form:tool-form-active { outline: 2px solid #4CAF50; }
button:tool-submit-active { background: #FFD700; }
```

### [User Interaction](https://github.com/webmachinelearning/webmcp/issues/21)

See [#21](https://github.com/webmachinelearning/webmcp/issues/21) for elicitation and user interaction support.

```javascript
execute: async ({ productId }, agent) => {
  // requestUserInteraction API - see #21 (https://github.com/webmachinelearning/webmcp/issues/21)
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
| **Anssi Kostiainen** | W3C / Intel | CG Chair, manages process, coordinates with TAG and AAIF ([#35](https://github.com/webmachinelearning/webmcp/issues/35)), a11y meta ([#65](https://github.com/webmachinelearning/webmcp/issues/65)), spec draft ([PR #64](https://github.com/webmachinelearning/webmcp/pull/64)), acks ([PR #72](https://github.com/webmachinelearning/webmcp/pull/72)), WebIDL conversion ([PR #75](https://github.com/webmachinelearning/webmcp/pull/75)), API syntax updates ([PR #37](https://github.com/webmachinelearning/webmcp/pull/37)), MCP intersection ([PR #46](https://github.com/webmachinelearning/webmcp/pull/46)), service workers ([PR #19](https://github.com/webmachinelearning/webmcp/pull/19)) | [GitHub](https://github.com/anssiko) |
| **Brandon Walderman** | Microsoft | Spec co-author, initial explainer ([PR #1](https://github.com/webmachinelearning/webmcp/pull/1)), service worker proposal ([PR #4](https://github.com/webmachinelearning/webmcp/pull/4)), elicitation design ([#21](https://github.com/webmachinelearning/webmcp/issues/21)), global naming ([#24](https://github.com/webmachinelearning/webmcp/issues/24)), API design ([#15](https://github.com/webmachinelearning/webmcp/issues/15)), prompt injection ([#11](https://github.com/webmachinelearning/webmcp/issues/11)), ARIA reuse analysis ([#63 comment](https://github.com/webmachinelearning/webmcp/issues/63)) | [GitHub](https://github.com/bwalderman) · [LinkedIn](https://www.linkedin.com/in/brandonwalderman) |
| **Andrew Nolan** | Microsoft | Spec co-author | [LinkedIn](https://www.linkedin.com/in/andrewnolanaiproductleader) |
| **David Bokan** | Google | Spec co-author, capability discovery ([#8](https://github.com/webmachinelearning/webmcp/issues/8)), output schema ([#9](https://github.com/webmachinelearning/webmcp/issues/9)), external agents ([#16](https://github.com/webmachinelearning/webmcp/issues/16)), typo fix ([PR #34](https://github.com/webmachinelearning/webmcp/pull/34)) | [GitHub](https://github.com/bokand) |
| **Khushal Sagar** | Google | Spec co-author, Chrome implementation lead, SDK abstraction ([#32](https://github.com/webmachinelearning/webmcp/issues/32)), `createClient` proposal ([#51](https://github.com/webmachinelearning/webmcp/issues/51)), MCP intersection doc ([PR #36](https://github.com/webmachinelearning/webmcp/pull/36)), iframe tools ([#57](https://github.com/webmachinelearning/webmcp/issues/57)), tool side-effects ([#53](https://github.com/webmachinelearning/webmcp/issues/53)), cross-origin scope ([#52](https://github.com/webmachinelearning/webmcp/issues/52)), human-in-the-loop ([#27](https://github.com/webmachinelearning/webmcp/issues/27)), concurrent execution ([#47](https://github.com/webmachinelearning/webmcp/issues/47)), multi-agent ([#49](https://github.com/webmachinelearning/webmcp/issues/49)), AbortSignal ([#48](https://github.com/webmachinelearning/webmcp/issues/48)), form mapping ([#63](https://github.com/webmachinelearning/webmcp/issues/63)) | [GitHub](https://github.com/khushalsagar) · [LinkedIn](https://www.linkedin.com/in/khushal-sagar) |
| **Hannah Van Opstal** | Google | Spec co-author | [LinkedIn](https://ca.linkedin.com/in/hannah-van-opstal) |
| **Dominic Farolino** | Google | Spec editor, declarative API explainer ([PR #76](https://github.com/webmachinelearning/webmcp/pull/76)), `Accept: application/json` analysis ([PR #26 comment](https://github.com/webmachinelearning/webmcp/pull/26)), Permission Policy for iframes ([#57 comment](https://github.com/webmachinelearning/webmcp/issues/57)) | [GitHub](https://github.com/domfarolino) · [LinkedIn](https://www.linkedin.com/in/domfarolino) · [X](https://x.com/domfarolino) |
| **Victor Huang** | Google | Security & Privacy considerations ([PR #55](https://github.com/webmachinelearning/webmcp/pull/55), [PR #59](https://github.com/webmachinelearning/webmcp/pull/59)), threat model ([#45](https://github.com/webmachinelearning/webmcp/issues/45)), tool annotations ([#53](https://github.com/webmachinelearning/webmcp/issues/53)), agent identity ([#54](https://github.com/webmachinelearning/webmcp/issues/54)), Document Policy for iframes ([#57 comment](https://github.com/webmachinelearning/webmcp/issues/57)), WebBotAuth reference ([#54 comment](https://github.com/webmachinelearning/webmcp/issues/54)) | [GitHub](https://github.com/victorhuangwq) |
| **Mark Foltz** | Google | Spec editor, file attachments ([#81](https://github.com/webmachinelearning/webmcp/issues/81)), streaming arguments ([#82](https://github.com/webmachinelearning/webmcp/issues/82)), Bikeshed build system ([PR #77](https://github.com/webmachinelearning/webmcp/pull/77), [PR #78](https://github.com/webmachinelearning/webmcp/pull/78), [PR #79](https://github.com/webmachinelearning/webmcp/pull/79), [PR #80](https://github.com/webmachinelearning/webmcp/pull/80)) | [GitHub](https://github.com/markafoltz) |
| **Reilly Grant** | Google | WebExtensions API proposal ([#74](https://github.com/webmachinelearning/webmcp/issues/74)), `navigator` placement advocacy ([#24 comment](https://github.com/webmachinelearning/webmcp/issues/24)), component coordination concern ([#15 comment](https://github.com/webmachinelearning/webmcp/issues/15)), `modelContextTesting` mention ([#74 comment](https://github.com/webmachinelearning/webmcp/issues/74)) | [GitHub](https://github.com/reillyeon) |
| **Anders Ruud** | Google | Declarative API deep dive — forms, radio buttons, labels ([#66](https://github.com/webmachinelearning/webmcp/issues/66), [#67](https://github.com/webmachinelearning/webmcp/issues/67), [#68](https://github.com/webmachinelearning/webmcp/issues/68), [#69](https://github.com/webmachinelearning/webmcp/issues/69), [#71](https://github.com/webmachinelearning/webmcp/issues/71)) | [GitHub](https://github.com/andruud) |
| **Francois Beaufort** | Google | Chrome Early Preview author, user gesture activation ([#62](https://github.com/webmachinelearning/webmcp/issues/62)), `navigator.modelContextTesting` interim API ([#74 comment](https://github.com/webmachinelearning/webmcp/issues/74)), JS examples ([PR #61](https://github.com/webmachinelearning/webmcp/pull/61)) | [GitHub](https://github.com/beaufortfrancois) |
| **Alexandra Klepper** | Google | Chrome Early Preview author | [LinkedIn](https://www.linkedin.com/in/alexandraklepper) · [Mastodon](https://mstdn.social/@alexandrawhite) |
| **Andre Bandarra** | Google | Chrome Early Preview author, [travel demo](https://travel-demo.bandarra.me/) | [GitHub](https://github.com/andreban) · [LinkedIn](https://uk.linkedin.com/in/andreban) · [X](https://x.com/andreban) · [Blog](https://bandarra.me/) |
| **Alex Nahas** | Amazon (prev.) | Original WebMCP concept, [MCP-B](https://mcp-b.ai) creator, declarative HTML proposal ([PR #26](https://github.com/webmachinelearning/webmcp/pull/26)), async registration ([#30](https://github.com/webmachinelearning/webmcp/issues/30)), streaming arguments ([#82 comment](https://github.com/webmachinelearning/webmcp/issues/82)) | [GitHub](https://github.com/MiguelsPizza) · [LinkedIn](https://www.linkedin.com/in/alex-nahas) |
| **Jason McGhee** | Independent | Early WebMCP implementation, external MCP client API ([#23](https://github.com/webmachinelearning/webmcp/issues/23)) | [GitHub](https://github.com/jasonjmcghee) · [LinkedIn](https://www.linkedin.com/in/jasonjmcghee) · [X](https://x.com/_jason_today) |
| **Ilya Grigorik** | Shopify | Commerce perspective, agent-to-agent interaction ([#31](https://github.com/webmachinelearning/webmcp/issues/31)) | [GitHub](https://github.com/igrigorik) |
| **Leonie Watson** | TPGi | Accessibility expert, screen reader perspective ([#65 comment](https://github.com/webmachinelearning/webmcp/issues/65)) | [GitHub](https://github.com/LJWatson) |

## Contributing

See [contributing.md](contributing.md) for guidelines. Only submit resources **directly about the WebMCP browser standard** (`navigator.modelContext` API or `toolname`/`tooldescription` HTML attributes). Do not submit general server-side MCP resources. (Follow [awesome-list standards](https://github.com/sindresorhus/awesome) for entry quality.)

## Maintainers

- Yigit Konur [@yigitkonur](https://x.com/yigitkonur)
- Web-MCP.net Crew [@web-mcp.net](https://web-mcp.net)

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0)

To the extent possible under law, the maintainers have waived all copyright and related or neighboring rights to this work.
