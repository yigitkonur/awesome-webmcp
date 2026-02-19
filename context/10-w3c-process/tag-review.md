# W3C TAG Review

> Technical Architecture Group engagement with WebMCP, October–November 2025.

---

## Overview

The **W3C Technical Architecture Group (TAG)** provides architectural review and guidance for web specifications. WebMCP's TAG engagement began in October 2025 and culminated in a formal presentation at TPAC 2025 in Kobe, Japan (November 11, 2025).

The TAG review is tracked in [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35).

---

## Timeline

| Date | Event | Reference |
|------|-------|-----------|
| Oct 9, 2025 | TAG member Xiaocheng opens Issue #35 proposing lower-level approach | [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Oct 24, 2025 | Anssi coordinates TAG communication; initial feedback shared | [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Nov 11, 2025 | TAG presents feedback at TPAC F2F session | TPAC minutes |
| Nov 11, 2025 | **RESOLUTION**: Continue development as high-level API per TAG guidance | [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35) |

---

## TAG Reviewers

### Xiaocheng

**GitHub**: [xiaochengh](https://github.com/xiaochengh)

Xiaocheng was the primary TAG reviewer for WebMCP. He proposed the **"lower-level primitives" approach** — building WebMCP from foundational capabilities (agent connection, permissions) rather than directly introducing a high-level API like `navigator.modelContext`.

His perspective was that a bottom-up approach would:
- Allow more flexibility in how different agent protocols are supported
- Create reusable primitives that other specs could build on
- Avoid over-commitment to a single protocol (MCP)

### Marcos Caceres

**GitHub**: [marcoscaceres](https://github.com/marcoscaceres)

Marcos Caceres expressed interest in attending the review and is listed as a TAG member engaged with WebMCP. His involvement signals broader TAG attention to the agentic web platform.

---

## TAG's Five Key Points

The TAG's review covered five areas:

### 1. Motivation — ✅ Supported

The TAG affirmed that frontend agent integration is useful and should be supported by the web platform. There was no pushback on the fundamental premise that websites should be able to expose structured capabilities to AI agents.

### 2. Generality — ⚠️ Concern

The TAG emphasized that the API should support **different protocols, not just MCP**. The concern was that a web standard named "WebMCP" and designed around MCP-specific concepts might not survive protocol evolution.

Response from the CG:
- The SDK-style abstraction layer ([Issue #32](https://github.com/webmachinelearning/webmcp/issues/32)) decouples the API from specific MCP versions
- Anthropic's migration of MCP to the Agentic AI Foundation provides broader governance

### 3. Privacy & Security — ⚠️ More Work Needed

The TAG requested more exploration of the privacy and security implications. This feedback directly led to Victor Huang's comprehensive security document ([PR #55](https://github.com/webmachinelearning/webmcp/pull/55)), filed three days after TPAC.

### 4. Declarative API — ✅ Strongly Endorsed

The TAG was particularly supportive of the declarative approach, noting:
> "Particularly useful as MCP might need to interface with the OS statically (without the web app running)"

This endorsement strengthened the push for the declarative API that resulted in Dominic Farolino's formal explainer ([PR #76](https://github.com/webmachinelearning/webmcp/pull/76)).

### 5. Approach: Bottom-Up vs Top-Down — ⚠️ Split Opinion

The TAG was split:

| Approach | Proponents | Argument |
|----------|-----------|----------|
| **Bottom-up** (lower-level primitives) | Xiaocheng | Build reusable foundations first |
| **Top-down** (high-level API directly) | CG consensus | Browsers are already shipping agentic features |

Khushal Sagar pushed back on the bottom-up approach:
> "We've already hit that wall. Multiple browsers are building agentic browsing."

---

## The MCP Longevity Concern

The TAG's most provocative statement:
> "MCP is not going to last."

This reflects a legitimate architectural concern: web standards need to outlast any specific protocol. The CG addressed this through:

1. **SDK abstraction**: The API is designed as a browser-native abstraction, not a direct MCP binding
2. **Protocol governance**: MCP moved to the Agentic AI Foundation under Linux Foundation stewardship
3. **Naming resilience**: `navigator.modelContext` is generic enough to survive protocol evolution
4. **Apple's input at TPAC**: "Names should stand the test of tens of years"

---

## TAG Endorsement

Despite the split on approach, the TAG's overall engagement was **constructive and supportive**. The CG characterized the outcome as an endorsement:

**RESOLUTION (Nov 11, 2025)**: Continue development as high-level API per TAG guidance.

This resolution was critical because it gave the CG confidence to proceed with their approach rather than starting over with lower-level primitives.

---

## Coordination with AI Agent Protocol CG

The TAG review also highlighted the need for coordination between WebMCP and the **AI Agent Protocol CG**, which works on agent communication protocols. The TAG recommended that:

- WebMCP focus on the browser-to-page interface
- Agent-to-agent protocols be handled by the AI Agent Protocol CG
- Both groups coordinate to ensure compatibility

---

## Impact on Specification

The TAG review influenced several spec decisions:

| TAG Feedback | Spec Response |
|--------------|---------------|
| Protocol generality | SDK abstraction layer (Issue #32) |
| Security concerns | Security & Privacy doc (PR #55) |
| Declarative endorsement | Formal declarative explainer (PR #76) |
| Bottom-up vs top-down | Continue as high-level API (Resolution) |
| Cross-origin complexity | Scoped out of v1 (Issue #52) |

---

## How TAG Reviews Work

For context, the W3C TAG review process:

1. **Request**: A CG/WG requests TAG review by filing an issue in the TAG's repository
2. **Assignment**: TAG assigns reviewers (typically 2-3 members)
3. **Review period**: Reviewers examine the spec, explainer, and repository
4. **Feedback**: TAG provides written feedback and/or presents at a meeting
5. **Follow-up**: The CG addresses feedback and may request a follow-up review

TAG reviews are advisory, not blocking. A CG can proceed without TAG endorsement, but positive TAG feedback significantly strengthens a spec's credibility within W3C.

---

## Links

| Resource | URL |
|----------|-----|
| TAG Review Issue | [github.com/webmachinelearning/webmcp/issues/35](https://github.com/webmachinelearning/webmcp/issues/35) |
| W3C TAG | [w3.org/2001/tag/](https://www.w3.org/2001/tag/) |
| SDK Abstraction | [github.com/webmachinelearning/webmcp/issues/32](https://github.com/webmachinelearning/webmcp/issues/32) |

---

## See Also

- [TPAC 2025](tpac-2025.md) — Full TPAC F2F session details
- [Meeting Resolutions](meeting-resolutions.md) — All CG resolutions
- [Spec Process](spec-process.md) — How the spec is developed
- [Khushal Sagar](../09-people/khushal-sagar.md) — Pushed back on bottom-up approach
- [Victor Huang](../09-people/victor-huang.md) — Security response to TAG feedback
- [Dominic Farolino](../09-people/dominic-farolino.md) — Declarative API response to TAG endorsement
- [Other Contributors](../09-people/other-contributors.md) — Xiaocheng and Marcos Caceres profiles
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Specification overview](../02-specification/spec-overview.md) — Specification overview
- [Threat model overview](../04-security/threat-model-overview.md) — Threat model overview
- [Privacy leakage concerns](../04-security/privacy-leakage.md) — Privacy leakage concerns
- [Other Contributors](../09-people/other-contributors.md) — Xiaocheng and Marcos Caceres profiles
- [Security and privacy discussions](../13-spec-discussions/security-privacy-trust.md) — Security and privacy discussions
- [Google Chrome's role](../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [WebMCP timeline](../01-overview/timeline.md) — WebMCP timeline
