# Specification

### Browser Support

| Browser | Status | Version | How to Enable |
|---|---|---|---|
| **Chrome** | Early Preview | 146.0.7672.0+ (Canary) | `chrome://flags` → "WebMCP for testing" |
| **Edge** | Expected | TBD | Same Chromium flag |
| **Firefox** | Not yet | — | — |
| **Safari** | Not yet | — | — |

```javascript
if ("modelContext" in navigator) {
  // WebMCP is supported
}
```

### Spec Documents

| Document | Link | Description |
|---|---|---|
| **Normative Spec** | [webmachinelearning.github.io/webmcp](https://webmachinelearning.github.io/webmcp) | Full spec: [`navigator.modelContext`](https://webmachinelearning.github.io/webmcp#navigator.modelContext), [`registerTool()`](https://webmachinelearning.github.io/webmcp#registerTool), [`provideContext()`](https://webmachinelearning.github.io/webmcp#provideContext), events, CSS pseudo-classes |
| **Source Repo** | [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) | Bikeshed spec, explainers, [security/privacy docs](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md), issue tracker |
| **Chrome Early Preview** | [developer.chrome.com](https://developer.chrome.com/blog/webmcp-epp) | Setup instructions, API overview, demo walkthrough |
| **Declarative API Explainer** | [PR #76](https://github.com/webmachinelearning/webmcp/pull/76) | HTML form-based tools: [`toolname`](https://github.com/webmachinelearning/webmcp/pull/76), [`tooldescription`](https://github.com/webmachinelearning/webmcp/pull/76), [`toolautosubmit`](https://github.com/webmachinelearning/webmcp/pull/76) |
| **Service Workers** | [service-workers.md](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) | Background tool execution without a visible tab |
| **Security & Privacy** | [security-privacy.md](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) | [Prompt injection](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#prompt-injection), [misrepresentation](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#misrepresentation), [over-parameterization](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#privacy-leakage) |
| **Deep Discussion Analysis** | [spec-discussions/](spec-discussions/) | All 55 issues + 20 PRs analyzed by theme |

### Key Spec Issues

| Topic | Issue | Author | Status |
|---|---|---|---|
| Accessibility via agentic interfaces | [#65](https://github.com/webmachinelearning/webmcp/issues/65) | [@anssiko](https://github.com/anssiko) | Open |
| In-page agent API (`executeTool` vs `createClient`) | [#51](https://github.com/webmachinelearning/webmcp/issues/51) | [@khushalsagar](https://github.com/khushalsagar) | Open |
| Elicitation / `requestUserInteraction` | [#21](https://github.com/webmachinelearning/webmcp/issues/21) | [@bwalderman](https://github.com/bwalderman) | Resolved |
| Tool annotations & side-effect hints | [#53](https://github.com/webmachinelearning/webmcp/issues/53) | [@victorhuangwq](https://github.com/victorhuangwq) | Open |
| Agent identity | [#54](https://github.com/webmachinelearning/webmcp/issues/54) | [@EmLauber](https://github.com/EmLauber) | Deferred |
| WebExtensions API | [#74](https://github.com/webmachinelearning/webmcp/issues/74) | [@reillyeon](https://github.com/reillyeon) | Open |
| Iframe tool registration | [#57](https://github.com/webmachinelearning/webmcp/issues/57) | [@khushalsagar](https://github.com/khushalsagar) | Open |
| User gesture activation | [#62](https://github.com/webmachinelearning/webmcp/issues/62) | [@beaufortfrancois](https://github.com/beaufortfrancois) | Open |
| File attachments | [#81](https://github.com/webmachinelearning/webmcp/issues/81) | [@markafoltz](https://github.com/markafoltz) | Open |
| Streaming arguments | [#82](https://github.com/webmachinelearning/webmcp/issues/82) | [@MiguelsPizza](https://github.com/MiguelsPizza) | Open |
| Scope clarification | [#43](https://github.com/webmachinelearning/webmcp/issues/43) | — | Open |

### Key Resolutions

Formal decisions from W3C CG meetings ([minutes archive](https://github.com/webmachinelearning/meetings)):

| Date | Resolution | Issue |
|---|---|---|
| Sep 2025 | **Human-in-the-loop first.** Automation deferred until security model matures. | [#27](https://github.com/webmachinelearning/webmcp/issues/27) |
| Sep 2025 | **SDK abstraction.** Browser translates between page and MCP client; decoupled from MCP version. | [#32](https://github.com/webmachinelearning/webmcp/issues/32) |
| Oct 2025 | **`navigator.modelContext`** is the root object name. | [#24](https://github.com/webmachinelearning/webmcp/issues/24) |
| Oct 2025 | **Tools as discovery.** Declarative API enables crawling/indexing without JS. | [#8](https://github.com/webmachinelearning/webmcp/issues/8) |
| Oct 2025 | **Both [`provideContext()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-providecontext) and [`registerTool()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-registertool)** supported. | [#15](https://github.com/webmachinelearning/webmcp/issues/15) |
| Oct 2025 | **No JS injection** by external agents. Extension API / CDP preferred. | [#16](https://github.com/webmachinelearning/webmcp/issues/16) |
| Oct 2025 | **[`requestUserInteraction()`](https://webmachinelearning.github.io/webmcp/#dom-navigatormodelcontext-requestuserinteraction)** for elicitation. Block abusive sites permanently. | [#21](https://github.com/webmachinelearning/webmcp/issues/21) |
| Oct 2025 | **No user-takeover notification** needed. Elicitation handles it. | [#20](https://github.com/webmachinelearning/webmcp/issues/20) |
| Nov 2025 | **TAG endorsement.** Continue as high-level API. Coordinate with AI Agent Protocol CG. | [#35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Nov 2025 | **Cross-origin scoped out of v1.** Same-origin tool registration only. | [#52](https://github.com/webmachinelearning/webmcp/issues/52) |
| Nov 2025 | **Agent identity deferred.** No clear v1 need. | [#54](https://github.com/webmachinelearning/webmcp/issues/54) |
| Jan 2026 | **Declarative complements imperative.** HTML form attributes + JS API. | [PR #26](https://github.com/webmachinelearning/webmcp/pull/26) |
| Jan 2026 | **[`AbortSignal`](https://webmachinelearning.github.io/webmcp/#dom-executetoolparams) for cancellation.** Tool callbacks receive abort signal. | [#48](https://github.com/webmachinelearning/webmcp/issues/48) |
| Jan 2026 | **Concurrent execution punted.** Depends on in-page agent API design. | [#47](https://github.com/webmachinelearning/webmcp/issues/47) |
| Feb 2026 | **[`outputSchema`](https://webmachinelearning.github.io/webmcp/#dom-toolregistration-outputschema) added.** Structured output definitions for tools. | [#9](https://github.com/webmachinelearning/webmcp/issues/9) |
