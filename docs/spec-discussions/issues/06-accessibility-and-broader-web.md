# Accessibility, TAG Review & Broader Web Platform

Issues: #65, #35, #28, #6, #7, #10, #29, #41, #82, #81

---

## Accessibility (Issue #65)

Filed by Anssi Kostiainen, with engagement from **Leonie Watson** (TPGi), a leading accessibility expert.

### Screen Readers & WebMCP

Leonie Watson's analysis:
> "Screen readers depend on platform accessibility API for deterministic information about the UI. AI agents also use the DOM and the accessibility API, and so both screen readers and agents are vulnerable when sufficient information is unavailable."

> "AI agents also use computer vision to analyze screenshots of the UI. Screen readers are increasingly using AI for this purpose."

> "For example, the JAWS screen reader has commands for identifying and labelling interactive elements that are missing an accessible name, and for requesting image descriptions from either ChatGPT or Claude."

> "It's possible that screen readers could benefit from WebMCP in the same way that agents are intended to."

This issue is open and represents a significant opportunity: WebMCP tools could serve as a better-than-DOM interface for assistive technologies, not just AI agents.

---

## TAG Communication (Issue #35)

The W3C Technical Architecture Group engaged significantly with WebMCP.

### TAG's Five Key Points

1. **Motivation**: Frontend agent integration is useful and should be supported
2. **Generality**: API should support different protocols, not just MCP
3. **Privacy & Security**: More exploration needed
4. **Declarative API**: Particularly useful; MCP might need to interface with OS statically
5. **No consensus on approach**: Split between:
   - Bottom-up: Build on lower-level primitives
   - Top-down: Directly introduce high-level API

### The MCP Longevity Concern

The TAG's loudest concern:
> "MCP is not going to last."

In response, Anthropic migrated MCP development to the **Agentic AI Foundation (AAIF)** under the Linux Foundation.

**RESOLUTION (TPAC Nov 2025)**: Continue development as high-level API per TAG guidance. Coordinate with AI Agent Protocol CG on new protocols.

### TAG Member Engagement

- **Xiaocheng**: Proposed "lower-level primitives" approach (agent connection, permissions)
- **Marcos Caceres**: Expressed interest in attending
- Khushal pushed back: "We've already hit that wall. Multiple browsers are building agentic browsing."

---

## MCP Protocol Alignment (Issue #28)

Formal process for coordinating with MCP spec:

**RESOLUTION (Sep 18, 2025)**: WebMCP provides SDK-style abstraction ("SDK option") between browser and MCP client.

**David Pang (Anthropic/MCP team)** engaged directly:
> "We are working towards defining MCP more as the data structures, slightly independent from the data-layer serialisation, like JSON-RPC. Would that help here?"

---

## Media Types: Images & Audio (Issue #41)

Khushal Sagar proposed supporting image input/output in tools.

**MiguelsPizza** provided MCP reference types (`ImageContent`, `AudioContent`, `EmbeddedResource`) and proposed browser-native conversion:

```js
navigator.modelContext.registerTool({
  name: "get_product_image",
  handler: async ({ productId }) => {
    const img = document.querySelector(`#product-${productId} img`);
    return { content: [{ type: "image", element: img }] };
  }
});
```

**Tom Nicholson** (Google) recommended aligning with Prompt API:
- Image input: `ImageBitmapSource`, `BufferSource`
- Audio input: `AudioBuffer`, `BufferSource`, `Blob`

---

## File Attachments (Issue #81)

Mark Foltz (Google, Feb 2026) proposed supporting file attachments via the imperative API. Very recent, Agenda+ label. No comments yet.

---

## Streaming Tool Arguments (Issue #82)

Alex Nahas (MiguelsPizza, Feb 2026) proposed streaming tool arguments -- the newest issue. Agenda+ label. No discussion yet.

---

## Output Schema (Issue #9)

Should tools have structured output schemas?

**RESOLUTION (Feb 5, 2026)**: Add `outputSchema` to imperative API. Investigate association with declarative API.

Brandon pointed to MCP's `outputSchema` discussion as prior art.

---

## Agent Memory / Resources (Issue #29)

Khushal filed this to discuss whether agents should be able to manage site-provided context (analogous to MCP "resources"). No resolution yet -- this represents the future scope beyond tools.

---

## Developer Tools (Issue #10)

Adam Sobieski filed this early issue about DevTools integration. Largely superseded by GoogleChromeLabs' `webmcp-tools` project and Chrome's Model Context Tool Inspector extension.

---

## MCP Client Capabilities (Issue #7)

Adam Sobieski proposed a `requestContext` API with well-known URIs for tool groups. No significant traction.

---

## Multi-Modal Auth Sessions (Issue #6)

Filed by awentzel about multi-modal authenticated session management. No discussion beyond the initial filing.
