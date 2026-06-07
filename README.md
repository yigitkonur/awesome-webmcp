# Awesome WebMCP

> A curated list of resources for WebMCP — the W3C web standard that lets websites expose structured tools to AI agents via `navigator.modelContext`.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Chrome 146+](https://img.shields.io/badge/Chrome-146+-blue.svg)](https://developer.chrome.com/blog/webmcp-epp)
[![W3C Draft](https://img.shields.io/badge/W3C-Community%20Draft-green.svg)](https://webmachinelearning.github.io/webmcp)


## Official Tooling

- [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) - Official Chrome Labs toolkit with Model Context Tool Inspector extension, WebMCP Evals CLI, React [flight-search demo](https://flight-search.firebaseapp.com) ([imperative](https://webmachinelearning.github.io/webmcp)), and restaurant reservation demo ([declarative](https://github.com/webmachinelearning/webmcp/pull/76)).
- [Model Context Tool Inspector](https://chromewebstore.google.com/detail/model-context-tool-inspec/gbpdfapgefenggkahomfgkhfehlcenpd) - Official Chrome extension for listing registered tools, executing manually, and testing with Gemini API.
- [WebMCP-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart) - Quickstart for connecting Chrome DevTools MCP to WebMCP tools with benchmark showing ~89% token reduction vs. screenshot automation.
- [WebMCP-org/webmcp-sh](https://github.com/WebMCP-org/webmcp-sh) - Source code for [webmcp.sh](https://webmcp.sh/) playground built with React 19, Vite, PGlite (in-browser Postgres), and Cloudflare Workers.

## Polyfills & Core Libraries

- [@mcp-b/global](https://www.npmjs.com/package/@mcp-b/global) - [Polyfill](https://webmachinelearning.github.io/webmcp) implementing [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) for browsers without native support. Drop-in replacement with the same API surface as the spec.
- [webmcp-core](https://www.npmjs.com/package/webmcp-core) - Zero-dependency [`navigator.modelContext`](https://github.com/webmachinelearning/webmcp/issues/24) polyfill (2.94 KB IIFE). Drop-in `@mcp-b/global` replacement with payment metadata support, SSR compatibility, and feature detection for native API fallback. 70 tests, TypeScript strict.
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
- [code-atlantic/webmcp-abilities](https://github.com/code-atlantic/webmcp-abilities) - WordPress plugin bridging the Abilities API to `navigator.modelContext.registerTool()`, exposing any registered ability as a structured tool for AI agents in Chrome 146+.
- [chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect) - WordPress plugin exposing WebMCP REST API. AI agents authenticate via JWT and invoke tools like `wordpress.searchPosts` and `wordpress.getPost`.
- [tuvit/webmcp](https://github.com/tuvit/webmcp) - Wix platform extension injecting WebMCP attributes into Wix Stores pages for AI agent access to e-commerce data.

## Payment

- [webmcp-payments](https://www.npmjs.com/package/webmcp-payments) - x402 payment acceptance middleware for WebMCP tools. Enables websites to charge AI agents per tool invocation using the HTTP 402 payment protocol with inline pricing metadata.

## Platforms

- [webmcp-platform](https://www.npmjs.com/package/webmcp-platform) - Unified WebMCP platform integrating polyfill, payments, and SDK into a single import. One-line setup for making websites agent-friendly with built-in monetization via x402.

## Demo Applications

- [Travel Demo](https://travel-demo.bandarra.me/) - Official Chrome team demo using `searchFlights` tool via [imperative API](https://webmachinelearning.github.io/webmcp). Part of the Chrome Early Preview docs.
- [Playground WebMCP](https://webmcp.sh/) - Interactive browser playground for connecting to MCP servers, running tools, and querying in-browser PostgreSQL. ([Source](https://github.com/WebMCP-org/webmcp-sh)).
- [isainative.dev](https://isainative.dev/) - Audit public GitHub repositories for AI coding readiness. Exposing both declarative and imperative WebMCP tools.
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
- [webmaxru/agent-skills: WebMCP](https://github.com/webmaxru/agent-skills/tree/main/skills/webmcp) - Agent skill with template assets for implementing and debugging browser WebMCP integrations in JavaScript or TypeScript apps.
- [opentiny/docs](https://github.com/opentiny/docs) - Unified VitePress documentation for the OpenTiny NEXT ecosystem covering `WebMcpClient`, `WebMcpServer`, MCP browser extensions, and related projects.

## Analysis & Readiness Tools

- [AI Agent Readiness Scanner](https://scanner.v1be.codes) ([source](https://github.com/ckorhonen/ai-agent-scanner)) - Free tool to score any website's AI agent readiness across 6 categories: WebMCP support, semantic HTML, structured data, llms.txt, crawlability, and content quality. Returns a grade (A–F), a 5-level readiness label (Invisible → AI-Native), per-check fix instructions with code examples, and supports side-by-side competitor comparison.

## Reference

Deep-dive material lives in [docs/](docs/): [Specification](docs/specification.md) | [API reference](docs/api-reference.md) | [Security](docs/security.md) | [Articles & talks](docs/reading.md) | [Community](docs/community.md) | [Key people](docs/key-people.md)

## Contributing

See [contributing.md](contributing.md) for guidelines. Only submit resources **directly about the WebMCP browser standard** (`navigator.modelContext` API or `toolname`/`tooldescription` HTML attributes). Do not submit general server-side MCP resources. (Follow [awesome-list standards](https://github.com/sindresorhus/awesome) for entry quality.)

## Maintainers

- Yigit Konur [@yigitkonur](https://x.com/yigitkonur)
- Web-MCP.net Crew [@web-mcp.net](https://web-mcp.net)

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0)

To the extent possible under law, the maintainers have waived all copyright and related or neighboring rights to this work.
