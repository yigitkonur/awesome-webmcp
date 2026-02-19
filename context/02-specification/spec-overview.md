# WebMCP Specification Overview

## Document Status

| Property | Value |
|---|---|
| **Status** | W3C Community Group Draft (CG-DRAFT), published 12 February 2026 |
| **Shortname** | webmcp |
| **Group** | [Web Machine Learning Community Group](https://www.w3.org/community/webmachinelearning/) (webml) |
| **Repository** | [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) |
| **Spec URL** | [webmachinelearning.github.io/webmcp](https://webmachinelearning.github.io/webmcp) |
| **Source Format** | [Bikeshed](https://github.com/webmachinelearning/webmcp/blob/main/index.bs) (.bs) |
| **Markup** | markdown yes, css no |

## Editors

| Name | Organization | Email | W3C ID |
|---|---|---|---|
| **Brandon Walderman** | Microsoft | brwalder@microsoft.com | 115877 |
| **Khushal Sagar** | Google | khushalsagar@google.com | 122787 |
| **Dominic Farolino** | Google | domfarolino@google.com | 102763 |
| **Mark Foltz** | Google | — | — |

## Abstract

> The WebMCP API enables web applications to provide JavaScript-based tools to AI agents.

WebMCP is a browser-native API that allows web developers to expose their web application
functionality as "tools" — JavaScript functions with natural language descriptions and
structured schemas that can be invoked by agents, browser's agents, and assistive technologies.

Web pages that use WebMCP can be thought of as [Model Context Protocol](https://modelcontextprotocol.io/introduction) (MCP) servers that
implement tools in client-side JavaScript instead of on the backend.

## How the Spec Is Built

The specification is authored in [Bikeshed](https://speced.github.io/bikeshed/) format,
which is a preprocessor for W3C specifications. The [source file is `index.bs`](https://github.com/webmachinelearning/webmcp/blob/main/index.bs) at the
repository root.

### Bikeshed Metadata Block

```
<pre class='metadata'>
Title: WebMCP
Shortname: webmcp
Level: None
Status: CG-DRAFT
Group: webml
Repository: webmachinelearning/webmcp
URL: https://webmachinelearning.github.io/webmcp
Editor: Brandon Walderman, Microsoft, brwalder@microsoft.com, w3cid 115877
Editor: Khushal Sagar, Google, khushalsagar@google.com, w3cid 122787
Editor: Dominic Farolino, Google, domfarolino@google.com, w3cid 102763
Abstract: The WebMCP API enables web applications to provide JavaScript-based tools to AI agents.
Markup Shorthands: markdown yes, css no
Complain About: accidental-2119 yes, missing-example-ids yes
Assume Explicit For: yes
Default Biblio Status: current
Boilerplate: omit conformance
Indent: 2
Die On: warning
</pre>
```

Key directives:
- **`Die On: warning`** — The spec build fails on any warnings, enforcing strict authoring.
- **`Complain About: accidental-2119 yes`** — Flags accidental use of RFC 2119 keywords (MUST, SHALL, etc.) outside normative contexts.
- **`Boilerplate: omit conformance`** — Omits the standard conformance section (early draft stage).

## Specification Structure

The Bikeshed spec is organized into these sections (see [full spec](https://webmachinelearning.github.io/webmcp)):

1. **Introduction** (`#intro`) — High-level overview, purpose, and positioning relative to MCP.
2. **Terminology** (`#terminology`) — Definitions of key terms: agent, browser's agent, AI platform.
3. **Security and Privacy Considerations** (`#security-privacy`) — Placeholder referencing the standalone security document.
4. **Accessibility Considerations** (`#accessibility`) — Placeholder for future accessibility guidance.
5. **API** (`#api`) — The normative API surface:
   - Extensions to the Navigator Interface (`#navigator-extension`)
   - ModelContext Interface (`#model-context-container`)
   - ModelContextOptions Dictionary (`#model-context-options`)
   - ModelContextTool Dictionary (`#model-context-tool`)
   - ModelContextClient Interface (`#model-context-client`)
6. **Acknowledgements** (`#acknowledgements`) — Credits to contributors.

## Key Terminology Defined in the Spec

### Agent
> An autonomous assistant that can understand a user's goals and take actions on the
> user's behalf to achieve them. Today, these are typically implemented by large language
> model (LLM) based AI platforms, interacting with users via text-based chat interfaces.

### Browser's Agent
> An agent provided by or through the browser that could be built directly into the
> browser or hosted by it, for example, via an extension or plug-in.

### AI Platform
> A provider of agentic assistants such as OpenAI's ChatGPT, Anthropic's Claude,
> or Google's Gemini.

## Normative References

The spec references two normative external specifications:

| Reference | Title | Publisher |
|---|---|---|
| **[[MCP]]** | [Model Context Protocol Specification](https://modelcontextprotocol.io/specification/latest) | The Linux Foundation |
| **[[JSON-SCHEMA]]** | [JSON Schema: A Media Type for Describing JSON Documents](https://json-schema.org/draft/2020-12/json-schema-core.html) | JSON Schema |

## Building the Spec Locally

The repository includes a `Makefile` for building the spec:

```bash
# Install Bikeshed
pip install bikeshed
bikeshed update

# Build the spec
make
# Or directly:
bikeshed spec index.bs
```

## Companion Documents

Beyond the normative Bikeshed spec, the repository contains several companion documents:

| Document | Path | Purpose |
|---|---|---|
| Explainer / README | `README.md` | High-level explainer for web developers |
| API Proposal | [`docs/proposal.md`](https://github.com/webmachinelearning/webmcp/blob/main/docs/proposal.md) | Detailed API design with examples |
| Security & Privacy | `docs/security-privacy-considerations.md` | Threat analysis and mitigations |
| Service Workers | [`docs/service-workers.md`](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) | Extension for background tool handling |

## Acknowledgements

The spec acknowledges these contributors for the initial explainer, proposals, and
discussions that established the foundation:

- [Brandon Walderman](https://github.com/webmachinelearning/webmcp/pull/1) (initial proposal, [PR #46](https://github.com/webmachinelearning/webmcp/pull/46))
- Leo Lee
- Andrew Nolan
- [David Bokan](https://github.com/webmachinelearning/webmcp/issues/9)
- [Khushal Sagar](https://github.com/webmachinelearning/webmcp/pull/75) ([PR #75](https://github.com/webmachinelearning/webmcp/pull/75) WebIDL)
- Hannah Van Opstal
- Sushanth Rajasankar

Additional thanks to Alex Nahas and Jason McGhee for sharing early implementation experience.

## Related Issues

- [Issue #24 — Bikeshedding the global name](https://github.com/webmachinelearning/webmcp/issues/24): Debate that led from `window.agent` to `navigator.modelContext`
- [Issue #22 — Declarative API Equivalent](https://github.com/webmachinelearning/webmcp/issues/22): Discussion on HTML-declarative alternatives to JS API
- [PR #64 — Spec draft](https://github.com/webmachinelearning/webmcp/pull/64): Initial spec draft in Bikeshed
- [PR #75 — WebIDL definitions](https://github.com/webmachinelearning/webmcp/pull/75): Formal WebIDL interface definitions
- [Chrome Early Preview Program](https://developer.chrome.com/blog/webmcp-epp): Chrome's WebMCP implementation announcement ([join EPP](https://developer.chrome.com/docs/ai/join-epp))

## See Also

- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [navigator.modelContext API](navigator-model-context.md)
- [registerTool() method](register-tool.md)
- [provideContext() method](provide-context.md)
- [unregisterTool() and clearContext()](unregister-tool.md)
- [Tool registration interface](tool-registration-interface.md)
- [WebIDL definitions](webidl-definitions.md)
- [MCP protocol intersection](mcp-intersection.md)
- [Declarative API overview](../03-declarative-api/overview.md)
- [Browser-agent model](../07-architecture/browser-agent-model.md)
- [Getting started tutorial](../12-tutorials/getting-started.md)
- [API reference](../15-reference/api-reference.md)
