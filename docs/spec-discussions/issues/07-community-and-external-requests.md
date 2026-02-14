# Community Requests, External Engagement & Misc

Issues: #40, #39, #38, #58, #30, #5, #17

---

## Where to Talk (Issue #40)

A recurring pain point. Communication is fragmented across:
- GitHub Issues (primary for async technical discussion)
- IRC (#webmachinelearning on W3C IRC)
- Discord (MCP community, Jason Mayes' Web AI server)
- W3C Slack
- Bi-weekly CG meetings (telecons)

**43081j** expressed frustration:
> "If we could settle on one for this space in particular, that would be extremely helpful. Currently discussion is scattered throughout meetings, IRC, Discord, external issues and so on. IRC seems inactive any time I'm there."

**Anssi's answer**: GitHub Issues for async technical discussion. Sync channels are fragmented "for better or worse."

**Jason Mayes** (Google) created a dedicated WebMCP channel in his Web AI Discord server (several hundred members).

---

## "Plea to Socialize" (Issue #58)

**maceip** filed a passionate request to broaden WebMCP's reach to the wider web ecosystem.

**Anssi's response**: Invited them to join the W3C Community Group and spread the word. Closed the issue as non-technical.

---

## WebLLM / LangGraph Support (Issue #39)

**am2222** asked about WebLLM or LangGraph integration. No substantive responses. Represents the broader community's desire to connect WebMCP with existing AI frameworks.

---

## Cloud AI Agent Support (Issue #38)

**ygupta81** asked about supporting cloud-based AI agents connecting to WebMCP tools.

**MiguelsPizza** redirected to existing frameworks:
> "Providing direct support for this would probably be better left to the library/framework space."

Pointed to:
- Assistant-UI's frontend tool implementation
- AG-UI's frontend tool implementation

Included helpful mermaid diagrams showing traditional vs frontend tool calling architectures.

---

## Async Dynamic Tool Registration (Issue #30)

**MiguelsPizza** proposed that tool registration return values could hint at tool set changes:

```js
execute: ({ a, b }) => {
  navigate("/new_page")
  return { content: [], unregisters: ["foo_tool"], registers: ["bar_tool"] }
}
```

**43081j** shared the practical problem they solved:
> "The reason we bumped into this was because a client might discover tools, which it then wants to call. When using the `list_tools`/`call_tool` pattern, this is fine. But if you try expose them as actual tools in the MCP server itself, the list won't be updated by the time it tries to call one."

This highlights the impedance mismatch between WebMCP's dynamic tool registration and MCP's relatively static tool discovery.

---

## Contributors (Issue #5)

Procedural issue for adding contributors. Anssi welcomed everyone and granted appropriate permissions.

---

## SVG Diagrams (Issue #17)

Filed by sohchatt about SVG design updates. Resolved by PR #18 (PNG conversion).

Tom Nicholson (Google) shared a tip about responsive SVGs with `prefers-color-scheme` media queries that work on GitHub.
