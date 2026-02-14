# Service Workers & Declarative Proposals

PRs: #19, #26, #76

---

## Service Worker Support (PR #19)

**Author**: Brandon Walderman (Microsoft/Edge)
**Filed**: Aug 28, 2025 | **Merged**: Sep 4, 2025
**Size**: +279 additions

### Core Idea

Service workers can register WebMCP tools that persist across navigations and work across multiple open tabs. This solves the problem Jason McGhee raised: what happens when you navigate away from a page that registered tools?

### MiguelsPizza's Detailed Response

Alex Nahas provided a comprehensive description of MCP-B's architecture:

> "The MCP-B extension aggregates WebMCP servers from all open tabs through the mcpHub in the extension's service worker. It's basically acting as an MCP host like Claude Desktop, but for all the open tabs which have WebMCP servers."

Key design insights shared:
- Extension service worker navigates to/focuses the tab that holds the tool
- Routes calls through content script to page's MCP server
- **Explicit consent model**: First visit to a site with tools requires domain AND tool approval
- **Hash-based verification**: If any part of a tool changes (params, schema, description), it must be re-approved
- Proposed **clipboard-style data isolation** for cross-origin sensitive data

### Brandon's Response on Auth Advantage

> "One advantage of using WebMCP for background tasks is that if the user is already logged in to the site/app then the WebMCP service worker will have those credentials. Otherwise, the service worker can open a tab for the user to authenticate."

> "Auth with remote MCP servers is still quite rough, and requires creating and managing auth tokens."

### Elicitation in Service Workers

Alex raised MCP's `elicitation` concept. Brandon opened Issue #21 separately.

---

## Declarative HTML Proposal (PR #26)

**Author**: MiguelsPizza (Alex Nahas)
**Filed**: Sep 17, 2025 | **Closed**: Feb 12, 2026 (in favor of #76)
**Size**: +507 additions

### The Longest-Running Discussion

This PR accumulated 14 substantive comments over nearly 5 months. It was the primary venue for:

1. **ARIA reuse debate** (Oct-Dec 2025)
2. **VOIX research integration** (Nov 2025)
3. **Mozilla feedback** (Jan 2026)
4. **Chrome prototyping announcement** (Jan 2026)
5. **Content negotiation concerns** (Dec 2025)

### Key Community Voices

**Sven Schultze (VOIX)** -- Joined after Anssi's outreach about his arxiv paper:
> "We established a more explicit interface where MCP tools are separated from standard UI HTML elements. This ensures the agent only accesses data and actions the developer specifically intended to share."

> "Is there an equivalent idea for declarative context/resources? We found that it was really helpful to explicitly set agent-only text elements."

**vsakaria** -- Raised iframe trust concerns:
> "The concern with the HTML method is of course the iFrame. Would an iFrame in a browser be more trustworthy?"

**Brandon (post-TPAC reversal on ARIA)**:
> "ARIA attributes for browsing agents was discussed at TPAC and the consensus is this is not actually a good idea."

### Chrome Prototyping

Anssi announced on Jan 22, 2026:
> "Chrome is now prototyping the proposal in this PR."

Link to Chromium commit: `3df80580897d24fbb730c0ee411f25ffe04e766b`

### Closure

Alex Nahas closed the PR himself on Feb 12, 2026:
> "I've reviewed the declarative explainer in #76 and it addresses many of my concerns. `RespondWith()` and `JSON-LD` extraction are solid solutions. I'll close this in favor of #76."

---

## Formal Declarative Explainer (PR #76)

**Author**: Dominic Farolino (Google Chrome)
**Filed**: Feb 5, 2026 | **Status**: OPEN (Agenda+)
**Size**: +120 additions

This is the active declarative proposal that superseded PR #26. No comments yet as of Feb 13, 2026. It introduces:
- `RespondWith()` for controlling what the agent receives
- JSON-LD extraction from form responses
- A formal separation from the content negotiation approach in PR #26
