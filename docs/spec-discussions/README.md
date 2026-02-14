# WebMCP Spec Discussion Analysis

Deep analysis of all issues (55) and pull requests (20) from [`webmachinelearning/webmcp`](https://github.com/webmachinelearning/webmcp) -- the official W3C Community Group specification repository.

**Data collected**: February 13, 2026
**Coverage**: Every open and closed issue and PR, read in full with all comments.

---

## Files

### Timeline
- [`00-timeline.md`](./00-timeline.md) -- Complete chronological timeline of milestones, resolutions, and key participant map

### Issue Groups (by theme)
- [`issues/01-api-surface-and-naming.md`](./issues/01-api-surface-and-naming.md) -- How `navigator.modelContext` was chosen, `provideContext` vs `registerTool`, the "jailed API" debate
- [`issues/02-security-privacy-and-trust.md`](./issues/02-security-privacy-and-trust.md) -- Threat model, prompt injection, misrepresentation of intent, annotations, action permissions, input limits, agent identity
- [`issues/03-declarative-api.md`](./issues/03-declarative-api.md) -- HTML forms as tools, ARIA reuse rejection, VOIX research, Anders Ruud's deep dive on form controls
- [`issues/04-elicitation-and-user-interaction.md`](./issues/04-elicitation-and-user-interaction.md) -- `requestUserInteraction`, AbortSignal, user gesture activation, commerce perspective
- [`issues/05-concurrency-multiagent-and-scope.md`](./issues/05-concurrency-multiagent-and-scope.md) -- Concurrent execution, cross-origin frames, iframe tools, agent-to-agent, human-in-the-loop focus
- [`issues/06-accessibility-and-broader-web.md`](./issues/06-accessibility-and-broader-web.md) -- Accessibility (screen readers), TAG review, MCP alignment, media types, output schema, file attachments, streaming
- [`issues/07-community-and-external-requests.md`](./issues/07-community-and-external-requests.md) -- Communication channels, cloud agents, async tool registration, framework integration requests
- [`issues/08-webextensions-and-external-agents.md`](./issues/08-webextensions-and-external-agents.md) -- WebExtensions API, external MCP clients, the in-page agent API debate

### PR Groups (by theme)
- [`prs/01-spec-evolution.md`](./prs/01-spec-evolution.md) -- From initial explainer to formal Bikeshed spec draft, all editorial PRs
- [`prs/02-service-workers-and-declarative.md`](./prs/02-service-workers-and-declarative.md) -- Service worker proposal, declarative HTML proposal (PR #26), formal declarative explainer (PR #76)

---

## Key Resolutions (all from meeting minutes)

| Date | Resolution | Issue |
|------|-----------|-------|
| Sep 18, 2025 | WebMCP focuses on human-in-the-loop use cases initially | #27 |
| Sep 18, 2025 | SDK abstraction layer between browser and MCP client | #32 |
| Oct 2, 2025 | `navigator.modelContext` is the root object name | #24 |
| Oct 2, 2025 | Tools should be part of the discovery mechanism | #8 |
| Oct 2, 2025 | Support both `provideContext` and `registerTool`/`unregisterTool` | #15 |
| Oct 2, 2025 | Higher-level hooks for external agents; no JS injection | #16 |
| Oct 2, 2025 | `requestUserInteraction` design for yielding to user | #21 |
| Oct 16, 2025 | Block abusive sites permanently; throw error for fallback | #21 |
| Oct 16, 2025 | No concrete use case for user-takeover notification | #20 |
| Nov 11, 2025 | Continue as high-level API per TAG guidance | #35 |
| Nov 11, 2025 | Cross-origin agents scoped out of v1 | #52 |
| Nov 11, 2025 | Agent identity: revisit later if needed | #54 |
| Jan 22, 2026 | Pursue declarative approach complementing imperative API | PR #26 |
| Jan 22, 2026 | Use AbortSignal for tool cancellation | #48 |
| Jan 22, 2026 | Concurrent tool execution punted for later | #47 |
| Feb 5, 2026 | Add outputSchema to imperative API | #9 |

---

## Open Design Questions (as of Feb 13, 2026)

1. **In-page agent execution API** (#51) -- `executeTool()` vs `createClient()` vs something else?
2. **WebExtensions API** (#74) -- Dedicated extension API for tool enumeration/invocation?
3. **Iframe tool registration** (#57) -- Permission Policy vs Document Policy?
4. **Declarative form mapping** (#63, #66-69, #71) -- Radio buttons, multiple labels, date constraints
5. **User gesture activation** (#62) -- Should agent tool calls get transient activation?
6. **File attachments** (#81) -- How to support file uploads via imperative API?
7. **Streaming arguments** (#82) -- Progressive delivery of tool arguments?
8. **Concurrent multi-agent execution** (#49) -- Multiple agents on same page?

---

## Community Health Indicators

- **55 issues** filed (30 open, 25 closed)
- **20 PRs** (16 merged, 2 closed, 2 open)
- **Key contributors**: 30+ unique participants from Google, Microsoft, Mozilla, W3C TAG, Anthropic, Shopify, TPGi, and independent developers
- **Meeting cadence**: Bi-weekly telecons since September 2025
- **TPAC 2025**: Full-day face-to-face session in Kobe, Japan (Nov 11, 2025)
- **Spec editors**: Brandon Walderman (Microsoft), Dominic Farolino (Google), Khushal Sagar (Google)
- **CG Chair**: Anssi Kostiainen (W3C/Intel)
