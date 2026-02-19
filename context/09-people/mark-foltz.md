# Mark Foltz

> **Google Chrome** · Spec Contributor · GitHub: [markafoltz](https://github.com/markafoltz)

---

## Role in WebMCP

Mark Foltz is a **significant spec contributor** for WebMCP, contributing primarily to the specification infrastructure and proposing new API capabilities. His work focuses on the build system, spec tooling, and expanding the API surface with features like file attachments and streaming.

> **Note**: Mark Foltz is not listed as a formal editor in the Bikeshed spec source (`index.bs`). The three listed editors are Brandon Walderman (Microsoft), Khushal Sagar (Google), and Dominic Farolino (Google). However, Mark's contributions to spec infrastructure and API expansion make him a key contributor.

---

## Key Contributions

### File Attachments Proposal (Issue #81)

Filed February 11, 2026, [Issue #81](https://github.com/webmachinelearning/webmcp/issues/81) proposes supporting **file attachment** capabilities in the imperative API. This would allow tools to accept and return file data — a capability important for document-processing agents, form-filling scenarios, and media handling.

The issue has the Agenda+ label and represents one of the newest API expansion proposals. As of February 2026, it is awaiting discussion in CG meetings.

### Build System & Spec Infrastructure (PRs #77–#80)

Mark significantly improved the spec development experience through a series of build tooling PRs:

| PR | What | Date |
|----|------|------|
| [PR #77](https://github.com/webmachinelearning/webmcp/pull/77) | Added Makefile for Bikeshed build quality of life | Feb 5, 2026 |
| [PR #78](https://github.com/webmachinelearning/webmcp/pull/78) | Reverted Makefile (issue discovered) | Feb 5, 2026 |
| [PR #79](https://github.com/webmachinelearning/webmcp/pull/79) | Updated Makefile approach | Feb 6, 2026 |
| [PR #80](https://github.com/webmachinelearning/webmcp/pull/80) | Cherry-picked final Makefile version | Feb 10, 2026 |

The Makefile enables local spec builds without relying on the CI/CD pipeline, improving the edit-preview cycle for spec editors. While the sequence of PRs shows some iteration ("Makefile saga"), the end result was a clean build system that makes spec development more accessible.

### Streaming Discussion (Issue #82)

Mark is engaged in the streaming tool arguments discussion filed by Alex Nahas ([Issue #82](https://github.com/webmachinelearning/webmcp/issues/82)), which relates to his file attachments proposal — both features deal with handling large data payloads in tool invocations.

---

## Background

Mark Foltz is a Google Chrome engineer with extensive experience in web platform standards. He has contributed to media-related specifications and Chrome's presentation and media playback capabilities. His contribution style focuses on infrastructure and enabling others — setting up build systems, proposing API extensions, and ensuring the spec is maintainable.

His contributions show Google's investment in WebMCP's spec infrastructure. While the three formal editors (Khushal Sagar, Brandon Walderman, Dominic Farolino) drive the API design, Mark's focus on infrastructure and API expansion complements their work:
- **Khushal Sagar**: Core API design and Chrome implementation
- **Brandon Walderman**: Initial explainer and Microsoft alignment
- **Dominic Farolino**: Declarative API and standards conformance
- **Mark Foltz**: Spec infrastructure and API expansion

---

## Design Philosophy

Mark's contributions reflect a pragmatic engineering approach:

1. **Developer experience**: Build tooling (Makefile) reduces friction for spec contributors
2. **Capability expansion**: File attachments and streaming address real-world agent needs
3. **Infrastructure quality**: Iterative improvement (PRs #77-#80) rather than one-shot perfection
4. **Multimodal support**: File attachments enable document-processing and form-filling agents

---

## File Attachments Use Cases

The file attachments proposal ([Issue #81](https://github.com/webmachinelearning/webmcp/issues/81)) enables several important agent workflows:

| Use Case | Description |
|----------|-------------|
| **Document upload** | Agent uploads a PDF/image to a form via tool invocation |
| **Receipt generation** | Tool returns a generated document as a file attachment |
| **Image processing** | Agent sends an image to a tool that analyzes or transforms it |
| **Data export** | Tool exports structured data as a downloadable file |
| **Form filling** | Agent attaches files to form submissions (insurance claims, applications) |

This proposal connects to Khushal Sagar's media types work ([Issue #41](https://github.com/webmachinelearning/webmcp/issues/41)) and Tom Nicholson's Prompt API alignment recommendations.

---

## Notable Contributions Timeline

| Date | Action | Reference |
|------|--------|-----------|
| Feb 5, 2026 | Makefile for Bikeshed builds | [PR #77](https://github.com/webmachinelearning/webmcp/pull/77) |
| Feb 5–10, 2026 | Makefile iteration and finalization | PRs #78, #79, #80 |
| Feb 11, 2026 | File attachments proposal | [Issue #81](https://github.com/webmachinelearning/webmcp/issues/81) |

---

## Links

| Resource | URL |
|----------|-----|
| GitHub | [github.com/markafoltz](https://github.com/markafoltz) |
| Issue #81 (File Attachments) | [github.com/webmachinelearning/webmcp/issues/81](https://github.com/webmachinelearning/webmcp/issues/81) |
| PR #77 (Makefile) | [github.com/webmachinelearning/webmcp/pull/77](https://github.com/webmachinelearning/webmcp/pull/77) |

---

## See Also

- [Khushal Sagar](khushal-sagar.md) — Co-editor, API design lead
- [Dominic Farolino](dominic-farolino.md) — Co-editor, declarative API
- [Alex Nahas](alex-nahas.md) — Streaming tool arguments (Issue #82)
- [Spec Process](../10-w3c-process/spec-process.md) — How the spec is built (Bikeshed, CI/CD)
- [Anssi Kostiainen](anssi-kostiainen.md) — CG Chair, initial spec draft (PR #64)
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Specification overview](../02-specification/spec-overview.md) — Specification overview
- [TAG review process](../10-w3c-process/tag-review.md) — TAG review process
- [TPAC 2025](../10-w3c-process/tpac-2025.md) — TPAC 2025
- [Security and privacy discussions](../13-spec-discussions/security-privacy-trust.md) — Security and privacy discussions
- [Google Chrome's role](../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [WebMCP timeline](../01-overview/timeline.md) — WebMCP timeline
- [Threat model overview](../04-security/threat-model-overview.md) — Threat model overview
- [Browser agent model](../07-architecture/browser-agent-model.md) — Browser agent model
