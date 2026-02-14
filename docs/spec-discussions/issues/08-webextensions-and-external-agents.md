# WebExtensions, External Agents & the Bridge Question

Issues: #74, #23, #32, #51

---

## WebExtensions API (Issue #74, Feb 2026)

Filed by **Reilly Grant** (Google Chrome), this is one of the newest and most impactful issues. Should there be a dedicated WebExtensions API for enumerating and invoking WebMCP tools?

### The Gap

Chrome's current implementation exposes tool registration but not tool execution in the public API. Extensions that want to use WebMCP tools need to inject content scripts and deal with world isolation issues.

**43081j** asked the obvious question:
> "Chrome and the spec are currently missing an execute function/method on the context. Once that exists, is a WebExtensions-specific API still needed?"

**Reilly Grant** explained why:
> "Ideally an extension could invoke a tool without injecting a content script into the page at all. At least in Chromium, JavaScript objects created by the main page aren't accessible to content scripts, only the DOM."

**Victor Huang** supported:
> "This would be much needed. There are already many browser automation extensions (including one by Claude) that rely on scraping the DOM and/or using screenshots."

### Current State

**Francois Beaufort** revealed Chrome's interim approach: `navigator.modelContextTesting` -- a separate testing API that includes list and execute capabilities.

**43081j** asked why this isn't part of `navigator.modelContext`:
> "Chrome calling the underlying internal execute function is an optimization. Chrome's agent and every other type of agent should call the same execute function at the end of the day."

No clear answer yet. This is Agenda+ for upcoming meetings.

---

## External MCP Client API (Issue #23)

Filed by **Jason McGhee** early on. Brandon outlined three agent categories:

1. **1st-party in-browser agent**: Internal browser APIs, flexible permission prompting
2. **3rd-party in-browser agent (extensions)**: Extension manifest permissions, extension APIs for listing/executing tools
3. **External desktop agents**: Native app messaging bridge, highest risk

Brandon on the risk:
> "This is the riskiest option since it potentially opens up a malicious page to the full power of a user's PC."

**MiguelsPizza** proposed Chrome DevTools Protocol as the bridge:

```bash
google-chrome \
  --remote-debugging-port=9222 \
  --enable-mcp-over-cdp \
  --mcp-permissions="prompt" \
  --mcp-allowed-domains="localhost,*.github.com"
```

Warning from Alex about the MCP-B native bridge risk:
> "You might forget you have the MCP-B local MCP proxy connected to your editor. It silently collects tools from all tabs you have open, and you might not even be aware that a malicious website is prompt injecting your editor session."

---

## Non-Browser Agent Integration (Issue #32)

Two approaches considered:

1. **Agent executes script**: Discovers tools via same Web APIs the site uses
2. **Higher-level hooks**: Extension API or CDP provides dedicated tool enumeration/execution

**RESOLUTION (Oct 2, 2025)**: Higher-level hooks preferred. JavaScript injection by external agents is NOT supported.

Key reasoning from Khushal:
> "If we support script injection, security policies and permissions will have to be designed for every consumer of these tools to faithfully apply. If the browser is arbitrating, it can ensure only 1 task from 1 agent is running at a time."

---

## In-Page Agent API (Issue #51)

The most debated technical issue in the repo. Can a JavaScript-based agent running ON the page call the same page's WebMCP tools?

### Community Voices

**43081j** (strong opinion):
> "I noticed the current draft and Chrome's flagged implementation don't include a way to list or execute tools. Did we get anywhere with this?"

> "This isn't a testing thing. Chrome should be exposing `{registerTool, executeTool, listTools}`. Chrome's own agent calling the underlying internal execute function is an optimization."

**KenjiBaheux** (Google):
> "The site might want to expose some tools to the browser agent, and a different set to other agents."

**43081j** pushed back:
> "If I run the Salesforce UI, I want to expose a `listTickets` function. I shouldn't have to care what agent YOU, the user, are using. You've just moved the burden to the web author instead of the agent."

**MiguelsPizza** added enterprise perspective:
> "Existing libraries that implement frontend tool calling might want to support WebMCP from a consumer standpoint. It would be worth exploring tool annotations that whitelist which agent can see and execute the annotated tool."

### Khushal's `createClient` Proposal

```js
let client = navigator.modelContext.createClient({agent: siteAgent});
console.log(client.tools);
client.execute(myTool.name, "{arg1: arg1_value}");
```

**Dominic Farolino** noted:
> "The basic capability @43081j wants IS provided with the above proposal. We can Bikeshed how much indirection is needed."

### Current Status

Open with Agenda+ label. The fundamental question remains: should the in-page execution API be as simple as `navigator.modelContext.executeTool("name", args)` or require a full `createClient()` with agent context?
