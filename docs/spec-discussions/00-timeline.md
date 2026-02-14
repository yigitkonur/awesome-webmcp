# WebMCP Specification Timeline

A chronological record of every significant milestone in the `webmachinelearning/webmcp` repository, compiled from all issues, PRs, and meeting resolutions.

---

## 2025

### August 2025 -- Genesis

| Date | Event | Ref |
|------|-------|-----|
| Aug 5 | Brandon Walderman (Microsoft/Edge) opens PR #1 with the initial explainer for "Script Tools API" | PR #1 |
| Aug 6 | PR #1 merged -- the first public commit of the spec | PR #1 |
| Aug 7 | Anssi Kostiainen (W3C/Intel) updates README to reflect new name "WebMCP" | PR #4 |
| Aug 8 | Repo renamed from its original name to `webmcp` after naming discussion in Issue #2 | Issue #2 |
| Aug 13 | Explainer content consolidated into README.md | PR #14 |
| Aug 19 | Authors & date added; Leo Lee (Google) fixes typos | PR #12, #13 |
| Aug 22 | Brandon proposes service worker support for persistent tools | PR #19 |
| Aug 25 | Service worker explainer merged | PR #19 |
| Aug 27 | SVG diagrams converted to PNG for light/dark mode compatibility | PR #18 |
| Aug 28 | MiguelsPizza (Alex Nahas) posts detailed MCP-B architecture in PR #19 comment -- first major community implementation report |

### September 2025 -- Design Foundations

| Date | Event | Ref |
|------|-------|-----|
| Sep 4 | Service worker explainer merged after community discussion on human-in-the-loop vs automation | PR #19 |
| Sep 5 | Issue #21 (Elicitation) filed by Brandon -- one of the most consequential design discussions | Issue #21 |
| Sep 17 | Alex Nahas (MiguelsPizza) opens PR #26 -- Declarative HTML-based tool proposal | PR #26 |
| Sep 18 | **RESOLUTION**: WebMCP focuses on human-in-the-loop use cases initially | Issue #27 |
| Sep 18 | **RESOLUTION**: WebMCP provides an SDK-style abstraction layer between browser and MCP client | Issue #32 |
| Sep 18 | Process setup agreed: Agenda+ labels, IRC bot resolutions | Issue #28 |

### October 2025 -- API Shape Decisions

| Date | Event | Ref |
|------|-------|-----|
| Oct 2 | **RESOLUTION**: `navigator.modelContext` is the root object name | Issue #24 |
| Oct 2 | **RESOLUTION**: Tools should be part of the discovery mechanism; declarative API explored | Issue #8 |
| Oct 2 | **RESOLUTION**: provideContext + registerTool/unregisterTool both supported | Issue #15 |
| Oct 2 | **RESOLUTION**: Higher-level hooks for external agent integration (no JS injection) | Issue #16 |
| Oct 3 | Minko Gechev (Angular/Google) submits formatting PR #33 | PR #33 |
| Oct 9 | Khushal Sagar adds "Intersection with MCP" documentation | PR #36 |
| Oct 9 | API syntax updated to match group resolutions | PR #37 |
| Oct 16 | **RESOLUTION**: `requestUserInteraction` API; abusive sites get permanent blocking option | Issue #21 |
| Oct 16 | **RESOLUTION**: User takeover during tool execution -- no concrete use case identified yet | Issue #20 |
| Oct 21 | Rick Viscomi submits formatting corrections | PR #42 |
| Oct 24 | TAG communication begins; TAG shares initial feedback | Issue #35 |

### November 2025 -- TPAC & Security

| Date | Event | Ref |
|------|-------|-----|
| Nov 2 | Proposal.md updated based on all resolved API decisions | PR #46 |
| Nov 7 | TPAC 2025 F2F planned for Nov 11 in Kobe, Japan | Issue #35, meetings |
| Nov 11 | **TPAC F2F**: TAG presents feedback; multiple resolutions made |
| Nov 11 | **RESOLUTION**: Continue development as high-level API per TAG guidance | Issue #35 |
| Nov 11 | **RESOLUTION**: Revisit agent identity issue in a different proposal if needed | Issue #54 |
| Nov 11 | **RESOLUTION**: Cross-origin agents scoped out of v1 | Issue #52 |
| Nov 14 | Victor Huang opens PR #55 -- Security & Privacy considerations living document | PR #55 |
| Nov 25 | Sven Schultze (VOIX paper author) joins the community after Anssi's outreach | PR #26 comment |
| Nov 25 | VOIX insights shared: separate interface for agent vs a11y, declarative context elements | PR #26 |

### December 2025 -- Security Deep Dive

| Date | Event | Ref |
|------|-------|-----|
| Dec 3 | Brandon clarifies: ARIA reuse for agents rejected at TPAC due to conflict of interest risk | PR #26 |
| Dec 5 | Security & Privacy considerations doc merged (PR #55) | PR #55 |
| Dec 11 | Dominic Farolino raises concerns about `Accept: application/json` in declarative proposal | PR #26 |
| Dec 12 | "Tool implementation as attack targets" section added (PR #59) | PR #59 |

## 2026

### January 2026 -- Spec Draft & Declarative Progress

| Date | Event | Ref |
|------|-------|-----|
| Jan 6 | Francois Beaufort fixes JS examples in proposal | PR #61 |
| Jan 7 | Mozilla's Ben vanderSloot engages: "why not just have the agent do the same thing as the user?" | PR #26 |
| Jan 21 | Anssi opens PR #64 -- initial spec draft with Bikeshed CI/CD | PR #64 |
| Jan 22 | **RESOLUTION**: Pursue declarative approach complementing imperative API | PR #26 |
| Jan 22 | **RESOLUTION**: Use AbortSignal for tool cancellation | Issue #48 |
| Jan 22 | Chrome begins prototyping declarative proposal | PR #26 comment |
| Jan 26 | Spec draft PR merged; Khushal Sagar appointed as editor | PR #64 |
| Jan 30 | Acknowledgements added to spec | PR #72 |

### February 2026 -- Rapid Specification Work

| Date | Event | Ref |
|------|-------|-----|
| Feb 5 | WebIDL and descriptions added to spec draft (PR #75) | PR #75 |
| Feb 5 | Declarative API Explainer PR opened by Dominic Farolino | PR #76 |
| Feb 5 | **RESOLUTION**: Add outputSchema to imperative API | Issue #9 |
| Feb 5 | Makefile added for Bikeshed build quality of life | PR #77 |
| Feb 7 | Anders Ruud (Chrome) files 5 declarative issues (#63, #66-69, #71) -- deep dive into form mapping | Issues #63-71 |
| Feb 10 | Reilly Grant (Chrome) files #74 -- WebExtensions API for tool enumeration/invocation | Issue #74 |
| Feb 10 | WebIDL PR merged | PR #75 |
| Feb 11 | Mark Foltz proposes file attachment support (#81) | Issue #81 |
| Feb 12 | Original declarative PR #26 closed in favor of formal explainer PR #76 | PR #26 |
| Feb 12 | Brandon Walderman's full spec PR #75 merged with Dominic's rebasing | PR #75 |
| Feb 13 | Alex Nahas proposes streaming tool arguments (#82) | Issue #82 |

---

## Key Participants

| Person | Affiliation | Role | Notable Contributions |
|--------|-------------|------|----------------------|
| **Anssi Kostiainen** (anssiko) | W3C / Intel | CG Chair | Runs meetings, manages process, coordinates with TAG and AAIF |
| **Khushal Sagar** (khushalsagar) | Google Chrome | Spec Editor, Collaborator | Core API design, Chrome implementation lead |
| **Brandon Walderman** (bwalderman) | Microsoft Edge | Spec Editor, Collaborator | Initial explainer, service worker proposal, Edge alignment |
| **Dominic Farolino** (domfarolino) | Google Chrome | Collaborator | Spec editing, declarative API explainer |
| **Alex Nahas** (MiguelsPizza) | MCP-B / Independent | Community Contributor | Original WebMCP OSS project, declarative HTML proposal, MCP annotations advocacy |
| **Jason McGhee** (jasonjmcghee) | Independent | Community Contributor | Early WebMCP implementation, external MCP client API ideas |
| **Victor Huang** (victorhuangwq) | Google | Contributor | Security & Privacy considerations document |
| **Reilly Grant** (reillyeon) | Google Chrome | Member | WebExtensions API, `navigator` placement advocacy |
| **43081j** | Independent | Community | Strong advocate for minimal web API, practical implementation experience |
| **Johann Hofmann** (johannhof) | Google Chrome | | Security model, user gesture activation, input length limits |
| **Francois Beaufort** (beaufortfrancois) | Google Chrome | Contributor | Code examples, Chrome testing, user gesture activation |
| **Ilya Grigorik** (igrigorik) | Shopify | | Commerce perspective, agent-to-agent, tool change notifications |
| **Xiaocheng** (xiaochengh) | W3C TAG | TAG Member | TAG review, lower-level primitives discussion |
| **Marcos Caceres** (marcoscaceres) | W3C TAG | TAG Member | TAG review interest |
| **Léonie Watson** (LJWatson) | TPGi | Accessibility Expert | Screen reader / accessibility perspective on WebMCP |
| **Anders Ruud** (andruud) | Google Chrome | | Declarative API deep dive -- forms, radio buttons, labels |
| **Mark Foltz** (markafoltz) | Google Chrome | Spec Editor | File attachments proposal, spec infrastructure |
| **Tom Nicholson** (tomayac) | Google | | SVG dark mode tip, Prompt API alignment for media types |
| **David Pang** (dsp-ant) | Anthropic / MCP | | MCP alignment, resources & prompts question |
