# Anssi Kostiainen

> **W3C / Intel** · Community Group Chair · GitHub: [anssiko](https://github.com/anssiko)

---

## Role in WebMCP

Anssi Kostiainen serves as the **Chair of the W3C Web Machine Learning Community Group**, the organizational home of the WebMCP specification. He is the primary process manager and facilitator, responsible for running meetings, managing the specification lifecycle, and coordinating with external W3C bodies including the Technical Architecture Group (TAG) and the AI Agent Interoperability Forum.

While not an author of the core API design, Anssi's contributions are foundational to WebMCP's existence as a standards-track effort rather than a vendor-specific proposal.

---

## Key Contributions

### Process & Governance

| Action | Date | Reference |
|--------|------|-----------|
| Updated README to reflect "WebMCP" name | Aug 7, 2025 | [PR #4](https://github.com/webmachinelearning/webmcp/pull/4) |
| Established meeting process: Agenda+ labels, IRC bot resolutions | Sep 18, 2025 | [Issue #28](https://github.com/webmachinelearning/webmcp/issues/28) |
| Coordinated TAG communication and review | Oct 24, 2025 | [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Organized TPAC 2025 face-to-face session in Kobe, Japan | Nov 11, 2025 | [Issue #35](https://github.com/webmachinelearning/webmcp/issues/35) |
| Opened initial spec draft with Bikeshed CI/CD pipeline | Jan 21, 2026 | [PR #64](https://github.com/webmachinelearning/webmcp/pull/64) |
| Added acknowledgements section | Jan 30, 2026 | [PR #72](https://github.com/webmachinelearning/webmcp/pull/72) |

### Spec Draft (PR #64)

PR #64 is arguably the most significant process milestone. Anssi seeded the Bikeshed source (`index.bs`) with content from the explainer and proposal documents, set up CI/CD for PR preview deploys, and appointed editors:

> "I have seeded `index.bs` with some content from explainer.md and proposal.md to help avoid the Blank Page Syndrome."
> "I will also appoint @khushalsagar as an editor for this spec."

This PR transitioned WebMCP from an informal explainer to a formal W3C Community Group Draft.

### WebIDL Integration (PR #75)

Brandon Walderman opened [PR #75](https://github.com/webmachinelearning/webmcp/pull/75) to add WebIDL interface definitions and descriptions to the spec draft, with Dominic Farolino performing the merge and rebasing. This was a critical step in making the spec normative and machine-readable. Anssi facilitated the resolution that led to this work.

### Community Outreach

- Reached out to **Sven Schultze** (VOIX paper author) to bring academic research into the declarative API discussion (Nov 2025, PR #26 comment)
- Responded to community socialization requests by inviting contributors to join the CG ([Issue #58](https://github.com/webmachinelearning/webmcp/issues/58))
- Managed contributor access and permissions ([Issue #5](https://github.com/webmachinelearning/webmcp/issues/5))
- Handled PR #34 (typo fix rejection) graciously, even consulting GitHub Copilot for grammar verification

### Accessibility Leadership

Filed [Issue #65](https://github.com/webmachinelearning/webmcp/issues/65) on accessibility, bringing in Leonie Watson (TPGi) for expert feedback on how WebMCP intersects with screen readers and assistive technologies.

---

## Background

Anssi Kostiainen has a long history with W3C, particularly in the Web Machine Learning space. He has chaired the Web Machine Learning Community Group through its work on the Web Neural Network API (WebNN) and other ML-related web standards. His dual affiliation with W3C and Intel positions him at the intersection of standards process and implementation.

His chairing style emphasizes:
- **Inclusive process**: Open to anyone joining the CG, welcoming community contributions
- **Resolution-driven meetings**: Uses formal IRC bot resolutions to capture decisions
- **Cross-group coordination**: Actively bridges WebMCP with TAG, AI Agent Interoperability Forum, and other CG/WGs

---

## Meeting Facilitation Style

From the specification discussions, Anssi's approach is visible:
- Uses **Agenda+** labels on GitHub Issues to queue topics for bi-weekly telcons
- Records formal **RESOLUTION** statements in meeting minutes
- Maintains bi-weekly cadence with occasional special sessions (e.g., TPAC F2F)
- Ensures decisions are reflected back into the spec and explainer documents

---

## Notable Quotes

On transitioning to a formal spec:
> (Response to 43081j's scope clarity requests in Issues #43, #56) — Anssi moved to create the formal Bikeshed spec draft, recognizing the community's need for a normative document.

On the Declarative API:
> "Chrome is now prototyping the proposal in this PR." — Jan 22, 2026, announcing Chrome's commitment to the declarative approach in PR #26

---

## Links

| Resource | URL |
|----------|-----|
| GitHub | [github.com/anssiko](https://github.com/anssiko) |
| W3C Profile | [w3.org/users/anssiko](https://www.w3.org/users/anssiko) |
| Web ML CG | [w3.org/community/webmachinelearning](https://www.w3.org/community/webmachinelearning/) |
| WebMCP Repo | [github.com/webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) |

---

## See Also

- [Community Group Overview](../10-w3c-process/community-group.md)
- [TPAC 2025](../10-w3c-process/tpac-2025.md)
- [TAG Review](../10-w3c-process/tag-review.md)
- [Spec Process](../10-w3c-process/spec-process.md)
- [Charter](../10-w3c-process/charter.md)
- [Brandon Walderman](brandon-walderman.md) — Spec co-editor
- [Khushal Sagar](khushal-sagar.md) — Spec co-editor
- [Dominic Farolino](dominic-farolino.md) — Spec co-editor
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Specification overview](../02-specification/spec-overview.md) — Specification overview
- [API naming discussions](../13-spec-discussions/api-surface-naming.md) — API naming discussions
- [Security and privacy discussions](../13-spec-discussions/security-privacy-trust.md) — Security and privacy discussions
- [Google Chrome's role](../14-industry/google-chrome-involvement.md) — Google Chrome's role
- [WebMCP timeline](../01-overview/timeline.md) — WebMCP timeline
- [Key milestones](../01-overview/key-milestones.md) — Key milestones
