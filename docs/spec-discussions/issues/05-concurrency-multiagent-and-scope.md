# Concurrency, Multi-Agent, and Scope Boundaries

Issues: #47, #49, #52, #57, #31, #27, #32

---

## Concurrent Tool Execution (Issue #47)

Should multiple tools run at the same time?

**43081j** argued it's not WebMCP's concern:
> "All these tools are regular functions. The author should take into account they can be called in quick succession. This proposal doesn't need to be concerned with userland architectural decisions."

> "We've had concurrency to deal with in MCP since the beginning because filesystems are stateful, and userland tools and agents solved that, not the MCP spec."

**RESOLUTION**: Punted for later. Depends on #51 (in-page agent API).

---

## Concurrent Execution by Multiple Agents (Issue #49)

What if the browser's built-in agent AND a Chrome extension agent both try to use tools simultaneously?

**MiguelsPizza** suggested annotations could help:
> "Tools with a `readOnly` annotation could be executed by multiple agents concurrently, but it's probably a premature optimization for a problem that does not exist yet."

**SuperPauly** contributed an extensive proposal for a `site_mcp_spec.json` handshake protocol covering:
- Per-tool concurrency limits
- Serial vs parallel tool execution
- RBAC-based tool exposure
- LLM policy negotiation (client-side vs server-side intelligence)
- Macro tool chains (50-100 tool sequences)

The proposal generated no direct responses from spec editors but represents the enterprise community's appetite for granular control.

---

## Cross-Origin Agents Across Frames (Issue #52)

Can an agent in a parent frame use tools from a cross-origin child iframe?

**43081j**: "Can we not just use existing security models? If a parent can access the window of a child frame, it can list and call tools."

**Khushal**: "There's a very limited set of properties that a cross-origin frame can access from other frames' `window` object as a security restriction."

**RESOLUTION (TPAC Nov 2025)**: Scoped out of v1. Discussion moved to #57 (iframes declaring tools).

---

## Iframes Declaring Tools (Issue #57)

After cross-origin agents were scoped out, the group pivoted to a narrower question: can iframes register tools at all?

**Dominic Farolino** advocated for Permission Policy:
> "Creating a permission policy so the top-level can delegate tool registration to a cross-origin iframe."

This is important for MCP Apps' `registerTool()` use case.

**Johann Hofmann** agreed but added caution:
> "Giving embedders the ability to direct the agent to use tools from other origins without those origins also agreeing to their usage might go against the same-origin-policy."

**Victor Huang** suggested **Document Policy** (two-way opt-in by both embedder and embeddee).

This issue remains open with Agenda+ label.

---

## Agent-to-Agent Interaction (Issue #31)

Filed by Khushal Sagar. Can the browser's built-in agent delegate tasks to an in-page agent?

**Ilya Grigorik** seeded this idea in Issue #24:
> "`window.agent.query()` as a generic 'let me ask you a question' and you decide how to fulfill it. Agent-to-agent should be a thing."

Khushal noted this could just be another tool:
> "The tool's implementation is a site's internal detail and it could be backed by an LLM."

---

## Human-in-the-Loop Focus (Issue #27, #32)

**RESOLUTION (Sep 18, 2025)**: WebMCP focuses on human-in-the-loop use cases initially.

From the meeting transcript:
> "Khushal: We've heard about 2 broad use case categories: 1) Automation, non-human-in-the-loop, 2) User and Agent sharing the UI, aka human-in-the-loop. Agreement to focus on the latter."
> "Alex: MCP-B is human-in-the-loop app first. When we figure out security story we can revisit later."

**Non-browser agents** (Issue #32):
**RESOLUTION (Oct 2, 2025)**: Higher-level hooks for external agents. No JavaScript injection by external agents.

---

## SDK Abstraction Layer (Issue #32)

**RESOLUTION (Sep 18, 2025)**: WebMCP provides a layer of abstraction between browser and MCP client ("SDK option").

Pros documented by Anssi:
- Decouples WebMCP from MCP version
- Allows different interaction patterns (e.g., elicitation)
- API ergonomics improvement (image/video element output)
- Makes declarative API easier

**Anthropic's David Pang** asked: "Is the plan to support resources and prompts as well?"

The group confirmed MCP primitives beyond tools are under consideration (Issue #29 for resources/context).
