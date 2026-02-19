# Alex Nahas

> **Independent** (previously Amazon) · MCP-B Creator · Community Contributor · GitHub: [MiguelsPizza](https://github.com/MiguelsPizza)

---

## Role in WebMCP

Alex Nahas is the **creator of the original WebMCP concept** through his open-source project **MCP-B** ([mcp-b.ai](https://mcp-b.ai)). His community implementation inspired and informed the W3C proposal, and he is one of the most prolific community contributors to the specification. His work bridges the gap between MCP's existing ecosystem and the emerging browser-native API.

Alex's contributions span the declarative HTML proposal, security innovations (clipboard-style data isolation), MCP annotations advocacy, and practical implementation experience from MCP-B.

---

## Key Contributions

### MCP-B: The Original WebMCP Implementation

MCP-B is a browser extension that turns web pages into MCP servers. Alex described the architecture in detail in a comment on [PR #19](https://github.com/webmachinelearning/webmcp/pull/19) (Aug 28, 2025):

> "The MCP-B extension aggregates WebMCP servers from all open tabs through the mcpHub in the extension's service worker. It's basically acting as an MCP host like Claude Desktop, but for all the open tabs which have WebMCP servers."

Key MCP-B design principles that influenced the spec:
- Extension service worker navigates to/focuses the tab that holds the tool
- Routes calls through content script to page's MCP server
- **Explicit consent model**: First visit to a site with tools requires domain AND tool approval
- **Hash-based verification**: If any part of a tool changes (params, schema, description), it must be re-approved

### Declarative HTML Proposal (PR #26)

Alex opened [PR #26](https://github.com/webmachinelearning/webmcp/pull/26) on September 17, 2025 — a comprehensive HTML-based tool proposal using `tool-*` attributes. This was the **longest-running discussion** in the repository, accumulating 14 substantive comments over nearly 5 months.

PR #26 served as the crucible for:
1. ARIA reuse debate (Oct–Dec 2025)
2. VOIX research integration (Nov 2025)
3. Mozilla feedback (Jan 2026)
4. Chrome prototyping announcement (Jan 2026)
5. Content negotiation concerns (Dec 2025)

Alex graciously closed the PR himself on Feb 12, 2026 in favor of Dominic Farolino's formal explainer ([PR #76](https://github.com/webmachinelearning/webmcp/pull/76)):
> "I've reviewed the declarative explainer in #76 and it addresses many of my concerns. `RespondWith()` and `JSON-LD` extraction are solid solutions. I'll close this in favor of #76."

### Async Dynamic Tool Registration (Issue #30)

Alex proposed that tool execution return values could hint at tool set changes:

```js
execute: ({ a, b }) => {
  navigate("/new_page")
  return { content: [], unregisters: ["foo_tool"], registers: ["bar_tool"] }
}
```

This addressed the impedance mismatch between WebMCP's dynamic tool registration and MCP's relatively static tool discovery.

### Streaming Tool Arguments (Issue #82)

Filed February 13, 2026 — one of the newest issues. Alex proposed streaming tool arguments for long-running tool invocations. Has Agenda+ label.

### Security: Clipboard-Style Data Isolation

Building on Brandon's prompt injection discussion ([Issue #11](https://github.com/webmachinelearning/webmcp/issues/11)), Alex formalized a data isolation model as an MCP SEP proposal with a full sequence diagram:
- Tool returns `ClipboardContent` with guard levels
- Client stores value, generates reference UUID
- LLM sees only reference, never raw sensitive data
- Resolution requires explicit user permission

### MCP Annotations Advocacy (Issue #53)

Alex strongly advocated for bringing MCP's annotation concept into WebMCP with **enforceable semantics**:

> "Annotations for WebMCP are much more exciting than they are for traditional MCP. The ability to give annotations enforceable semantics in a web standard is something I wish we had in MCP. A third party (UA) enforcing middleware on tool calls is something unique to WebMCP."

> "The browser could allow concurrent execution of tools marked as `readOnly` while serializing those marked `destructive`."

### Multi-Agent Concurrency (Issue #49)

On concurrent execution by multiple agents:
> "Tools with a `readOnly` annotation could be executed by multiple agents concurrently, but it's probably a premature optimization for a problem that does not exist yet."

### Enterprise Perspective (Issue #51)

Provided enterprise context for the in-page agent API:
> "Existing libraries that implement frontend tool calling might want to support WebMCP from a consumer standpoint. It would be worth exploring tool annotations that whitelist which agent can see and execute the annotated tool."

### Human-in-the-Loop Alignment

From the September 2025 resolution discussion:
> "MCP-B is human-in-the-loop app first. When we figure out security story we can revisit later."

### Native Bridge Warning

Alex raised an important security warning about MCP-B's local proxy:
> "You might forget you have the MCP-B local MCP proxy connected to your editor. It silently collects tools from all tabs you have open, and you might not even be aware that a malicious website is prompt injecting your editor session."

---

## MCP-B Architecture

```
┌─────────────────────────────────────────────┐
│  Browser Extension (MCP-B)                  │
│  ┌─────────────────────────────────────┐    │
│  │  Service Worker (mcpHub)            │    │
│  │  - Aggregates tools from all tabs   │    │
│  │  - Routes calls to correct tab      │    │
│  └─────────────────────────────────────┘    │
│           │                                  │
│  ┌────────┼────────────┐                    │
│  │  Tab 1 │ Tab 2      │ ...                │
│  │  Content Scripts     │                    │
│  │  → Page MCP Server   │                    │
│  └──────────────────────┘                    │
└─────────────────────────────────────────────┘
```

---

## Background

Alex Nahas previously worked at Amazon. He created MCP-B as an independent project and is credited in the WebMCP specification's acknowledgements section. His GitHub username `MiguelsPizza` appears throughout the repository's discussions.

He was interviewed on **Arcade.dev** about MCP-B and the vision for browser-native MCP. The interview, titled **"WebMCP: Making Every Website a Tool for AI Agents"**, covered the journey from the original open-source project to the W3C standardization effort.

**Arcade.dev interview URL**: [arcade.dev/blog/web-mcp-alex-nahas-interview](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview)

Key details from the interview:
- Alex was described as a "backend engineer at Amazon" when MCP arrived in early 2025
- Amazon "spun up what amounted to one enormous MCP server, providing thousands of tools, all crammed into a single context window"
- The real problem was authorization: "The MCP spec had adopted OAuth 2.1, which essentially nobody at Amazon had implemented internally"
- Alex realized the browser already handles auth through federated login, leading to MCP-B
- He "integrated the MCP TypeScript SDK into a client-side JavaScript application and built a custom transport using postMessage to communicate with a Chrome extension"

---

## Attribution

Alex requested and received attribution for the original open-source WebMCP project. This was granted via the acknowledgments section added in [PR #72](https://github.com/webmachinelearning/webmcp/pull/72):

> (From Issue #24 naming discussion) Alex requested attribution for the original open-source WebMCP project. Granted via acknowledgments section.

---

## Notable Quotes

On MCP-B's consent model:
> "The MCP-B extension aggregates WebMCP servers from all open tabs through the mcpHub in the extension's service worker."

On annotations:
> "Annotations for WebMCP are much more exciting than they are for traditional MCP."

On closing PR #26:
> "I've reviewed the declarative explainer in #76 and it addresses many of my concerns."

---

## Links

| Resource | URL |
|----------|-----|
| GitHub | [github.com/MiguelsPizza](https://github.com/MiguelsPizza) |
| MCP-B Project | [mcp-b.ai](https://mcp-b.ai) |
| Arcade.dev Interview | [arcade.dev/blog/web-mcp-alex-nahas-interview](https://www.arcade.dev/blog/web-mcp-alex-nahas-interview) |
| PR #26 (Declarative Proposal) | [github.com/webmachinelearning/webmcp/pull/26](https://github.com/webmachinelearning/webmcp/pull/26) |
| Issue #82 (Streaming Args) | [github.com/webmachinelearning/webmcp/issues/82](https://github.com/webmachinelearning/webmcp/issues/82) |
| Issue #30 (Async Registration) | [github.com/webmachinelearning/webmcp/issues/30](https://github.com/webmachinelearning/webmcp/issues/30) |

---

## See Also

- [Brandon Walderman](brandon-walderman.md) — Built on Alex's security ideas
- [Dominic Farolino](dominic-farolino.md) — Authored formal declarative explainer (PR #76)
- [Jason McGhee](jason-mcghee.md) — Another early community implementer
- [Ilya Grigorik](ilya-grigorik.md) — Commerce perspective, also community contributor
- [Other Contributors](other-contributors.md) — Full list of community participants
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Specification overview](../02-specification/spec-overview.md) — Specification overview
- [Declarative API overview](../03-declarative-api/overview.md) — Declarative API overview
- [Declarative API design debates](../13-spec-discussions/declarative-api-debates.md) — Declarative API design debates
- [API naming discussions](../13-spec-discussions/api-surface-naming.md) — API naming discussions
- [Google Chrome's role](../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [WebMCP timeline](../01-overview/timeline.md) — WebMCP timeline
- [W3C Community Group](../10-w3c-process/community-group.md) — W3C Community Group
- [Declarative API tutorial](../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
