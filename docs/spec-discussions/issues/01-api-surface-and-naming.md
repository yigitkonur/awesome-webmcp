# API Surface & Naming Decisions

Issues: #2, #24, #15, #16, #43, #56

---

## The Naming Journey

The spec went through multiple naming iterations:

1. **"Script Tools API"** -- Google's original internal name (PR #1, Aug 2025)
2. **Repo rename to `webmcp`** -- Aug 8, 2025 (Issue #2)
3. **Global object bikeshed** (Issue #24) -- extensive debate:
   - `window.mcp` -- early favorite, concerns about "P" in Protocol for a DOM API
   - `window.agent` -- rejected, could be misconstrued as "the site's agent"
   - `navigator.agentContext` -- Reilly Grant's suggestion; avoids global namespace pollution
   - `navigator.modelContext` -- final winner; mirrors "Model Context Protocol"

**RESOLUTION (Oct 2, 2025)**: `navigator.modelContext` is the root object name.

### Key arguments that shaped the decision:

- **Reilly Grant**: "`window` is the global namespace; anything placed there risks conflicts with JS obfuscators. `navigator` is safer."
- **Brandon Walderman**: "`agentContext` doesn't assume number or kind of agents. More generic than `toolRegistry` -- leaves room for resources, prompts."
- **Khushal Sagar**: WebGL parallel -- "WebMCP" name recognition value outweighs the awkwardness of "P" for a non-protocol API
- **Apple feedback at TPAC**: "Names should stand the test of tens of years"
- **Alex Nahas (MiguelsPizza)**: Requested attribution for the original open-source WebMCP project. Granted via acknowledgments section.

---

## API Design: provideContext vs registerTool

Issue #15 saw a fundamental split:

### Option 1: `provideContext({ tools: [...] })` (batch replacement)
- **Pro**: Good for React/SPAs -- compute available tools from state, batch-send
- **Pro**: Single API call means fewer change notifications to agent
- **Con**: Components from different teams stomp on each other's tools

### Option 2: `registerTool(options)` / `unregisterTool(name)` (incremental)
- **Pro**: Better for multi-component pages; mirrors `addEventListener` pattern
- **Pro**: @43081j's CustomElementRegistry analogy: `define`, `get`, `whenDefined`
- **Con**: More notifications, more coordination needed

**RESOLUTION (Oct 2, 2025)**: Support both. `provideContext` as primary, `registerTool`/`unregisterTool` as complement. Chromium implements both for experimentation.

### Notable quotes:

> "My concern with a single `provideContext` method is that real-world sites are built out of a collection of components which may or may not be well-coordinated. It seems likely that a single method which replaces the entire context makes it easy for developers to stomp on each others' usage." -- **Reilly Grant**

> "In our implementation we have a CustomElementsRegistry-style interface: `window.agent.tools.define(name, def)`" -- **43081j**

---

## Tool Execution & the "Jailed API" Debate

Issues #16, #51, #43 generated the most heated community discussion.

### The Core Tension

Chrome's initial implementation exposed `registerTool` but **not** `executeTool` on `navigator.modelContext`. Only Chrome's internal agent could call tools. Community members argued this was artificially restrictive.

**43081j** (independent implementer, multi-comment thread in #51):
> "Chrome has implemented this in a way that means ONLY Chrome can execute registered tools (internally). That obviously doesn't fly. Chrome should be calling the same code path that a public `executeTool` method calls."

> "What chrome has implemented is basically a nerfed webmcp API"

**Khushal Sagar** explained the rationale:
1. Concurrency management -- browser can ensure only 1 agent acts at a time
2. Per-agent tool visibility -- sites may want different tool sets per agent type
3. Elicitation coordination -- `requestUserInteraction` needs browser mediation

**RESOLUTION (Oct 2, 2025)**: No JS injection by external agents. Higher-level hooks preferred.

### The `createClient` Proposal

Khushal proposed a `ModelContextClient` interface:

```idl
interface ModelContextClient : EventTarget {
  readonly attribute sequence<ModelContextTool> tools;
  attribute EventHandler ontoolschanged;
  Promise<any> execute(DOMString name, DOMString arguments);
};
```

43081j pushed back:
> "You've lost me here, how come there's a concept of a 'client' now? This is really just a very simple registry of tools."

> "You and other Googlers are trying to design around something others (including me) are missing. Is it concurrency?"

The discussion remains open (Issue #51, Agenda+). The community is split between minimalist (43081j) and structured (Google) approaches.

---

## Scope Clarity

Issue #43 (43081j) and #56 (43081j) pushed for clearer scope definition:

> "Much of this reads more complicated than it needs to be. Defining the browser API would be enough that people can start building interfaces to that."

Anssi responded by transitioning from explainer to formal spec draft (Issue #60, PR #64).
