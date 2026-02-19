# Other Demos & Applications

> Additional WebMCP demo applications covering travel insurance, medical evidence, notes, readiness analysis, RAG pipelines, and more.

## Overview

Beyond the primary demos, the WebMCP community has produced several additional demonstration applications, each showcasing different use cases and integration patterns. This document catalogs these secondary demos with their key features and links.

---

## tsunoyu/webmcp-travel-insurance

**Travel insurance lifecycle demo** demonstrating complete policy management via WebMCP tools.

- **Repository**: [github.com/tsunoyu/webmcp-travel-insurance](https://github.com/tsunoyu/webmcp-travel-insurance)
- **Author**: [@tsunoyu](https://github.com/tsunoyu)

### Tools

| Tool | Description |
|------|-------------|
| `insurance.getQuote` | Get a travel insurance quote based on trip details |
| `insurance.purchasePolicy` | Purchase a travel insurance policy |
| `insurance.viewPolicy` | View an existing policy |
| `insurance.fileClaim` | Submit an insurance claim |
| `insurance.checkClaimStatus` | Check the status of a submitted claim |
| `insurance.cancelPolicy` | Cancel an active policy |

### Key Features

- Complete policy lifecycle: quoting → purchasing → claims → cancellation
- Uses `requestUserInteraction` for purchase and cancellation (financial actions)
- Demonstrates `destructiveHint` for policy cancellation
- Proper `readOnlyHint` on query tools

---

## Cycasio/medsyn-web

**Living evidence synthesis database** for medical research with WebMCP tools for searching and reviewing studies.

- **Repository**: [github.com/Cycasio/medsyn-web](https://github.com/Cycasio/medsyn-web)
- **Author**: [@Cycasio](https://github.com/Cycasio)

### Tools

| Tool | Description |
|------|-------------|
| `medsyn.searchStudies` | Search RCT studies by keyword, intervention, or outcome |
| `medsyn.getEvidence` | Get GRADE evidence summary for a specific topic |
| `medsyn.submitStudy` | Submit a new study for review |
| `medsyn.compareInterventions` | Compare effectiveness of different interventions |

### Key Features

- Medical domain-specific tool implementations
- GRADE evidence quality framework integration
- Structured evidence summaries in tool responses
- Read-only tools for searching; write tool for submissions

---

## alecron/webmcp-demo — Notes App

**Notes application** with 5 tools demonstrating `provideContext()` for batch tool registration.

- **Repository**: [github.com/alecron/webmcp-demo](https://github.com/alecron/webmcp-demo)
- **Author**: [@alecron](https://github.com/alecron)

### Tools

| Tool | Description |
|------|-------------|
| `notes.add` | Add a new note |
| `notes.list` | List all notes |
| `notes.search` | Search notes by keyword |
| `notes.delete` | Delete a note by ID |
| `notes.stats` | Get note statistics |

### Key Features

- Uses `navigator.modelContext.provideContext()` for batch registration
- Simple localStorage-based persistence
- Demonstrates the full CRUD pattern with search
- Minimal, clean implementation

---

## alanw707/webmcp-readiness-radar

**Site readiness assessment tool** scoring a website's WebMCP agent-readiness.

- **Repository**: [github.com/alanw707/webmcp-readiness-radar](https://github.com/alanw707/webmcp-readiness-radar)
- **Author**: [@alanw707](https://github.com/alanw707)

### Features

- Scans any URL for WebMCP adoption signals
- Pillar-wise breakdown:
  - **Tool Registration**: Are tools properly registered?
  - **Schema Quality**: Are input schemas well-defined?
  - **Descriptions**: Are descriptions clear and useful?
  - **Annotations**: Are side-effect hints provided?
  - **Declarative**: Are HTML form attributes used?
  - **Security**: Are destructive actions gated by user interaction?
- Overall readiness score (0-100)
- Specific recommendations for improvement
- Export results as JSON or PDF

---

## WebMCP-org/ai-tinkerers-webmcp-demo

**In-browser RAG pipeline** with document ingestion, embeddings, and semantic search via WebMCP tools.

- **Repository**: [github.com/WebMCP-org/ai-tinkerers-webmcp-demo](https://github.com/WebMCP-org/ai-tinkerers-webmcp-demo)
- **Maintained by**: WebMCP-org

### Tools

| Tool | Description |
|------|-------------|
| `rag.ingestDocument` | Ingest a document for embedding |
| `rag.search` | Semantic search across ingested documents |
| `rag.listDocuments` | List all ingested documents |
| `rag.deleteDocument` | Remove a document from the index |

### Tech Stack

- **Embeddings**: Transformers.js (in-browser ML)
- **Storage**: Dexie.js over IndexedDB
- **Vector search**: Cosine similarity in-browser
- **No backend**: Everything runs client-side

### Key Features

- Fully client-side RAG pipeline
- No API keys required (Transformers.js for embeddings)
- Documents stored in IndexedDB via Dexie
- Semantic search with cosine similarity
- Presented at AI Tinkerers meetup

---

## Additional Notable Demos

### SrinivasanTarget/WebMCP-demo

Full polyfill demo with 15+ sample tools and an AI agent simulator.

- **Repository**: [github.com/SrinivasanTarget/WebMCP-demo](https://github.com/SrinivasanTarget/WebMCP-demo)
- **Features**: Math tools, DOM tools, storage tools, network tools, JSON-RPC messaging simulator

### Legit-Control/webMCP-exploration

React app integrating WebMCP with Legit SDK for Git-like versioned state mutations.

- **Repository**: [github.com/Legit-Control/webMCP-exploration](https://github.com/Legit-Control/webMCP-exploration)
- **Features**: AI agents edit calendar in isolated branches with visual previews; merge/revert operations

### khushalsagar/webmcp-demo

Flight booking demo by Chrome spec co-author, showing both declarative and imperative approaches side-by-side.

- **Repository**: [github.com/khushalsagar/webmcp-demo](https://github.com/khushalsagar/webmcp-demo)
- **Author**: Khushal Sagar (Google, spec co-author)
- **Features**: Side-by-side declarative HTML vs imperative JS comparison

---

## Demo Comparison Matrix

| Demo | Tools | Framework | API Style | Persistence | Live Demo |
|------|-------|-----------|-----------|-------------|-----------|
| Travel Insurance | 6 | — | Imperative | Session | — |
| MedSyn Evidence | 4 | React | Imperative | Backend | — |
| Notes App | 5 | Vanilla JS | provideContext | localStorage | — |
| Readiness Radar | — | React | Scanner | — | — |
| RAG Pipeline | 4 | React | Imperative | IndexedDB | — |
| WebMCP-demo (15+) | 15+ | React | Polyfill | Memory | — |
| Legit Exploration | 5+ | React | Imperative | Legit SDK | — |
| Flight Booking | 3 | Vanilla JS | Both | Session | — |

## See Also

- [Travel Demo](./travel-demo.md) — Official Chrome team demo
- [Kanban Demo](./kanban-demo.md) — React kanban with 8 tools
- [DoorDash Starter](./doordash-starter.md) — Single-file zero-dependency demo
- [Calendar Demo](./calendar-demo.md) — Next.js calendar with 15 tools
- [Playground (webmcp.sh)](./playground-webmcp-sh.md) — Interactive WebMCP playground
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Getting started tutorial](../../12-tutorials/getting-started.md) — Getting started tutorial
- [Imperative API tutorial](../../12-tutorials/imperative-api-tutorial.md) — Imperative API tutorial
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Creative design use case](../../11-use-cases/creative-design.md) — Creative design use case
- [Developer sentiment](../../14-industry/developer-sentiment.md) — Developer sentiment
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
