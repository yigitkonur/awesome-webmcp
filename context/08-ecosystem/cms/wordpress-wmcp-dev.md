# wmcp.dev — WebMCP Optimization Platform

> **⚠️ Note**: wmcp.dev is a general-purpose AI web interaction optimization platform (by [dpx10](https://github.com/dpx10/webMCP)), NOT a WordPress-specific plugin. It helps make any website AI-agent-ready through token optimization and structured tool exposure.

## Overview

[wmcp.dev](https://www.wmcp.dev/) is a web platform and toolkit that optimizes websites for AI agent interaction. It processes web page elements and reduces token overhead, making sites more efficient for AI agents to understand and operate. The project reports ~67% average token reduction across major AI models.

- **Website**: [wmcp.dev](https://www.wmcp.dev/)
- **Source**: [github.com/dpx10/webMCP](https://github.com/dpx10/webMCP)
- **Focus**: AI web interaction optimization, token reduction, making websites AI-agent-ready
- **Status**: In production (97 tests passing as of Feb 2026)

## Key Features

### Token Optimization

Reduces the number of tokens AI agents need to process when interacting with web pages:

| AI Model | Original Tokens | Optimized Tokens | Reduction |
|----------|----------------|-------------------|-----------|
| GPT-4o | ~1,247 | ~412 | ~67% |
| Claude-3.5-Sonnet | ~1,183 | ~357 | ~70% |
| GPT-4 | ~1,156 | ~389 | ~66% |

### Processing Performance

- Sub-100ms processing time
- 90%+ cache hit rate with intelligent invalidation
- Low per-request cost (~$0.01 average)

## How It Differs from WebMCP Declarative API

wmcp.dev focuses on **optimizing existing pages for AI consumption**, while the W3C WebMCP declarative API (`toolname`, `tooldescription` attributes) focuses on **explicitly declaring tools** for agents. They address different but complementary aspects of making the web AI-accessible.

## See Also

- [WordPress wp-ai-connect](./wordpress-wp-ai-connect.md) — WordPress REST API for WebMCP
- [Wix WebMCP](./wix-webmcp.md) — Wix platform WebMCP extension
- [Declarative API (PR #76)](https://github.com/webmachinelearning/webmcp/pull/76) — W3C declarative spec
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Form mapping for declarative tools](../../03-declarative-api/form-mapping.md) — Form mapping for declarative tools
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [E-commerce use case](../../11-use-cases/e-commerce.md) — E-commerce use case
- [SEO implications](../../11-use-cases/seo-implications.md) — SEO implications
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Community feature requests](../../13-spec-discussions/community-external-requests.md) — Community feature requests
