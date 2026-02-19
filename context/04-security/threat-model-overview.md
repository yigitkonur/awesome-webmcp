# Threat Model Overview

> WebMCP Security & Privacy Threat Model — comprehensive risk assessment framework for browser-based AI agent tool exposure.

## Status

- **Spec Document**: `_spec/docs/security-privacy-considerations.md`
- **Primary Issues**: [#45](https://github.com/webmachinelearning/webmcp/issues/45), [#11](https://github.com/webmachinelearning/webmcp/issues/11), [#44](https://github.com/webmachinelearning/webmcp/issues/44)
- **PRs**: #55 (initial draft), #59 (tool implementation targets)
- **Lead Author**: Victor Huang (Google)
- **Contributors**: Johann Hofmann (Chrome), Khushal Sagar (Google), Brandon Walderman (Microsoft)

---

## Three Main Risk Categories

WebMCP's threat model identifies three primary categories of security and privacy risks:

### 1. Prompt Injection Attacks

Malicious instructions embedded in tool metadata, inputs, or outputs that manipulate agent behavior. Unlike traditional injection attacks, these exploit the language model's natural language interpretation rather than code execution vulnerabilities.

Three distinct attack vectors:

| Vector | Threat Actor | Target | Assets at Risk |
|--------|-------------|--------|----------------|
| **Metadata/Description Attacks** (Tool Poisoning) | Malicious websites | Agent reasoning & actions | User data, agent control, other sites |
| **Output Injection** | Malicious websites or untrusted UGC | Agent reasoning & actions | User data, agent control, other sites |
| **Tool Implementation as Targets** | Attackers controlling agents | Websites exposing tools | High-value actions (transactions, DB access) |

> **Key Risk Factor**: Agent decision-making relies on natural language interpretation, and tool descriptions/return values are treated as trusted context.

**Deep Dives**:
- [`prompt-injection.md`](./prompt-injection.md) — Full attack taxonomy
- [`tool-poisoning.md`](./tool-poisoning.md) — Metadata/description attacks
- [`output-injection.md`](./output-injection.md) — Return value injection

### 2. Misrepresentation of Intent

No guarantee that a tool's declared intent matches its actual behavior. Creates a fundamental trust gap where agents rely on natural language descriptions but cannot verify actual effects before execution.

Two misalignment types:
- **Malicious misrepresentation** (fraud): Deliberate deception to trick agents
- **Accidental misalignment**: Poorly written descriptions, semantic ambiguity

**Deep Dive**: [`misrepresentation.md`](./misrepresentation.md)

### 3. Privacy Leakage Through Over-Parameterization

Sites design highly parameterized tools to extract sensitive user data that agents provide from personalization context. Creates a personalization-to-fingerprinting pipeline.

**Deep Dive**: [`privacy-leakage.md`](./privacy-leakage.md)

---

## Agent Baseline Capabilities

The threat model assumes agents operate with certain baseline capabilities that significantly impact the security landscape:

### Identity Inheritance

Agents inherit user identity and authentication context from the browser. When an agent visits a website, it carries the user's logged-in credentials and session state.

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Browser    │────▶│    Agent     │────▶│   Website    │
│  (cookies,   │     │ (inherits   │     │  (sees full  │
│  sessions)   │     │  identity)  │     │  auth state) │
└─────────────┘     └─────────────┘     └─────────────┘
```

**Implication**: Even without sharing data through tool parameters, having authenticated state means tools can perform high-privilege actions — make purchases, transfer funds, modify account settings, share private data, delete content.

### Extended User Context

Agents may access personalization data to improve task completion:
- Browsing history
- Payment information
- User preferences and stored profiles
- Inferred attributes (location, demographics)

### Cross-Site Context

Agents can access and correlate information across multiple websites to fulfill user requests. This enables powerful user experiences but creates privacy risks when one site can extract context gathered from another.

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│ Weather   │     │  Agent   │     │ Shopping │
│  Site     │────▶│ (learns  │────▶│  Site    │
│ (location)│     │ location)│     │ (extracts│
└──────────┘     └──────────┘     │ location)│
                                   └──────────┘
```

---

## Approach to Risk Assessment

The security document evaluates risks with three key considerations:

### 1. All Entities Involved

The model considers roles and responsibilities of:
- **Site authors**: Implement tools, define descriptions, handle execution
- **Agent providers**: Interpret tools, make decisions, handle user data
- **Browser vendors**: Mediate access, enforce permissions, provide UI
- **End-users**: Grant consent, maintain expectations of safety

### 2. Spec Limitations and Responsibilities

The WebMCP spec cannot define precise mitigation strategies that agents/browser vendors must provide. Instead, it:
- Clearly defines responsibilities for each system
- Documents common mitigations as recommendations
- Explores mitigations to inform API additions

### 3. Alignment with MCP

Adopts relevant risk assessments and mitigations from MCP (Model Context Protocol) to inform WebMCP discussions. MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`) are relevant precedents.

---

## Threat Model Gaps Identified

Johann Hofmann (Chrome) pushed for clearer threat modeling, identifying three realistic threat scenarios:

1. **Cross-origin attacks**: Origins trying to enforce malicious action on other origins
2. **Third-party content attacks**: Third-party content in tool definitions attacking the first-party origin
3. **Agent/model attacks**: Origins using prompt injection to attack the agent directly (PII leakage, memory poisoning)

> **Johann Hofmann**: "Who do we consider a potential attacker, and what would be their goal? I'm not sure the first party developer of the page would have any particular incentive to force malicious action through WebMCP on their own site — they already control the page!"

---

## Open Questions

### Risk Identification
- Are there specific attack scenarios from existing web security domains (CSRF, XSS) that apply to WebMCP in novel ways?
- What risks emerge when combining WebMCP with other emerging web capabilities (Prompt API, Web AI)?

### Permission Models
- What granularity of user consent is appropriate for different tool types?
- How do we prevent permission fatigue while maintaining user control?
- Should some tool categories require elevated permissions? ([Issue #44](https://github.com/webmachinelearning/webmcp/issues/44))

### Threat Models
- Are there additional attack scenarios from existing web security domains?
- What risks emerge combining WebMCP with other emerging web capabilities?

---

## Security Document Acknowledgment

Initially drafted based on discussion points from:
- Victor Huang (Google)
- Khushal Sagar (Google)
- Johann Hofmann (Chrome)
- Emily Lauber (Microsoft)
- Dave Risney (Microsoft)
- Luis Flores (Microsoft)
- Andrew Nolan (Microsoft)

---

## See Also

- [Prompt injection attacks](prompt-injection.md)
- [Tool poisoning attacks](tool-poisoning.md)
- [Output injection attacks](output-injection.md)
- [Misrepresentation of intent](misrepresentation.md)
- [Privacy leakage](privacy-leakage.md)
- [Developer security checklist](developer-security-checklist.md)
- [User gesture activation](user-gesture-activation.md)
- [External security guides](external-security-guides.md)
- [Human-in-the-loop philosophy](../06-user-interaction/human-in-the-loop.md)
- [requestUserInteraction() API](../06-user-interaction/request-user-interaction.md)
- [Execute callback specification](../02-specification/execute-callback.md)
- [ToolAnnotations metadata](../02-specification/annotations.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)

## References

- [W3C TAG Security and Privacy Questionnaire](https://w3ctag.github.io/security-questionnaire/)
- [Prompt Injection: What's the worst that can happen?](https://simonwillison.net/2023/Apr/14/worst-that-can-happen/) — Simon Willison
- [WebMCP Security & Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy-considerations.md) — Living document
