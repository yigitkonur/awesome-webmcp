# Security Resources

### Official Threat Model

The [Security and Privacy Considerations](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) covers ([PR #55](https://github.com/webmachinelearning/webmcp/pull/55), [PR #59](https://github.com/webmachinelearning/webmcp/pull/59)):

1. **[Prompt Injection](https://github.com/webmachinelearning/webmcp/issues/45)** — Tool poisoning (malicious descriptions), output injection (tainted return values), tool-as-target (exploiting high-value tools).
2. **[Misrepresentation](https://github.com/webmachinelearning/webmcp/issues/53)** — Tools whose behavior doesn't match their description.
3. **Privacy Leakage** — Over-parameterized tools extracting unnecessary personal data.

### External Security Guides

- [MCP-B Security Best Practices](https://docs.mcp-b.ai/security) - Practical recommendations for WebMCP implementations.
- [Known Security Issues (MiguelsPizza Wiki)](https://github.com/MiguelsPizza/WebMCP/wiki/Known-Security-Issues-With-WebMCP) - Community-maintained attack vector list.
- [Protecting Against Indirect Injection (Microsoft)](https://developer.microsoft.com/blog/protecting-against-indirect-injection-attacks-mcp) - Applicable to WebMCP tool descriptions.
- [Mitigating Prompt Injections in Browser Use (Anthropic)](https://www.anthropic.com/research/prompt-injection-defenses) - Defense strategies for browser-based AI agents.

### Developer Checklist

- Validate all input in `execute()` — don't rely on schema alone.
- Use [`agent.requestUserInteraction()`](https://github.com/webmachinelearning/webmcp/issues/21) for destructive/financial actions.
- Check [`event.agentInvoked`](https://github.com/webmachinelearning/webmcp/issues/62) to differentiate agent vs human.
- Set [`readOnlyHint: true`](https://github.com/webmachinelearning/webmcp/issues/53) on read-only tools.
- Never return raw PII in tool responses ([privacy leakage](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md#privacy-leakage)).
- Implement rate limiting in tool handlers.
- Keep descriptions positive ("Search flights") not negative ("Don't use for hotels").
- Log all tool invocations for audit.
- Update UI after execution — agents verify state via DOM.
- Use [`toolautosubmit`](https://github.com/webmachinelearning/webmcp/pull/76) only for idempotent operations.
