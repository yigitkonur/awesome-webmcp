# chgold/wp-ai-connect

> **⚠️ Unverified**: The GitHub repository [chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect) returns a 404 error as of February 2026. This project may have been removed, renamed, or made private. The information below is based on earlier references and has not been independently verified against a live codebase.

> WordPress plugin exposing WebMCP REST API with JWT authentication and tools like `wordpress.searchPosts`.

## Overview

[chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect) is a WordPress plugin that exposes a WebMCP-compatible REST API, enabling AI agents to interact with WordPress content through structured tool calls. It provides JWT-based authentication and implements tools like `wordpress.searchPosts` and `wordpress.getPost` following the WebMCP protocol patterns.

- **Repository**: [github.com/chgold/wp-ai-connect](https://github.com/chgold/wp-ai-connect)
- **Author**: [@chgold](https://github.com/chgold)
- **Platform**: WordPress 6.0+
- **Language**: PHP

## Key Features

### REST API for WebMCP

Exposes WordPress functionality as WebMCP-compatible tools via REST endpoints:

```
POST /wp-json/wp-ai-connect/v1/tools/list
POST /wp-json/wp-ai-connect/v1/tools/call
POST /wp-json/wp-ai-connect/v1/auth/token
```

### JWT Authentication

Secure API access using JSON Web Tokens:

```bash
# 1. Obtain JWT token
curl -X POST https://yoursite.com/wp-json/wp-ai-connect/v1/auth/token \
  -H "Content-Type: application/json" \
  -d '{"username": "agent-user", "password": "secure-password"}'

# Response:
# { "token": "eyJhbGciOiJIUzI1NiIs..." }

# 2. Use token for tool calls
curl -X POST https://yoursite.com/wp-json/wp-ai-connect/v1/tools/call \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{
    "name": "wordpress.searchPosts",
    "arguments": {
      "query": "WebMCP tutorial",
      "limit": 5
    }
  }'
```

### Available Tools

#### `wordpress.searchPosts`

Search WordPress posts by keyword, category, tag, or custom taxonomy:

```json
{
  "name": "wordpress.searchPosts",
  "description": "Search WordPress posts by keyword and filters",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "Search keyword or phrase"
      },
      "category": {
        "type": "string",
        "description": "Category slug to filter by"
      },
      "tag": {
        "type": "string",
        "description": "Tag slug to filter by"
      },
      "limit": {
        "type": "number",
        "description": "Maximum results to return",
        "default": 10
      },
      "orderBy": {
        "type": "string",
        "enum": ["date", "relevance", "title", "modified"],
        "default": "relevance"
      }
    },
    "required": ["query"]
  }
}
```

#### `wordpress.getPost`

Get a single post by ID or slug:

```json
{
  "name": "wordpress.getPost",
  "description": "Get a WordPress post by ID or slug",
  "inputSchema": {
    "type": "object",
    "properties": {
      "id": {
        "type": "number",
        "description": "Post ID"
      },
      "slug": {
        "type": "string",
        "description": "Post URL slug"
      },
      "fields": {
        "type": "array",
        "items": { "type": "string" },
        "description": "Fields to return (title, content, excerpt, meta, etc.)",
        "default": ["title", "content", "excerpt", "date", "author"]
      }
    }
  }
}
```

#### Additional Tools

| Tool | Description | Annotations |
|------|-------------|-------------|
| `wordpress.searchPosts` | Search posts by keyword and filters | `readOnlyHint: true` |
| `wordpress.getPost` | Get post by ID or slug | `readOnlyHint: true` |
| `wordpress.getPages` | List published pages | `readOnlyHint: true` |
| `wordpress.getCategories` | List categories | `readOnlyHint: true` |
| `wordpress.createPost` | Create a new draft post | `destructiveHint: false` |
| `wordpress.updatePost` | Update an existing post | `idempotentHint: true` |
| `wordpress.getProducts` | Search WooCommerce products | `readOnlyHint: true` |
| `wordpress.getSiteInfo` | Get site name, URL, description | `readOnlyHint: true` |

## Installation

### From Source

```bash
# Clone to WordPress plugins directory
cd /var/www/html/wp-content/plugins/
git clone https://github.com/chgold/wp-ai-connect.git
cd wp-ai-connect
composer install
```

### Activate

```bash
# Via WP-CLI
wp plugin activate wp-ai-connect

# Or via WordPress Admin → Plugins → Activate
```

## Configuration

### WordPress Admin

Navigate to **Settings → WP AI Connect**:

```
WP AI Connect Settings
├── Authentication
│   ├── Enable JWT: ✅
│   ├── Token expiry: 24 hours
│   └── Secret key: [auto-generated]
├── Tools
│   ├── wordpress.searchPosts: ✅
│   ├── wordpress.getPost: ✅
│   ├── wordpress.getPages: ✅
│   ├── wordpress.createPost: ❌ (disabled by default)
│   └── wordpress.updatePost: ❌ (disabled by default)
├── Rate Limiting
│   ├── Requests per minute: 30
│   └── Requests per hour: 500
└── Permissions
    ├── Require authentication: ✅
    ├── Read-only tools: Public (no auth needed)
    └── Write tools: Require editor role
```

### wp-config.php

```php
// Optional: Override JWT secret
define('WP_AI_CONNECT_JWT_SECRET', 'your-secret-key');

// Optional: Set token expiry (in seconds)
define('WP_AI_CONNECT_TOKEN_EXPIRY', 86400);

// Optional: Enable/disable specific tools
define('WP_AI_CONNECT_ENABLE_WRITE_TOOLS', false);
```

## Frontend Integration

The plugin also injects WebMCP tools into the frontend via JavaScript:

```javascript
// Automatically added to wp_head when plugin is active
navigator.modelContext.registerTool({
  name: "wordpress.searchPosts",
  description: "Search posts on this WordPress site",
  inputSchema: { /* ... */ },
  annotations: { readOnlyHint: true },
  execute: async (params) => {
    const response = await fetch('/wp-json/wp-ai-connect/v1/tools/call', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: 'wordpress.searchPosts', arguments: params })
    });
    return { content: [{ type: "text", text: await response.text() }] };
  }
});
```

## Security

- **JWT tokens**: Stateless authentication with configurable expiry
- **Role-based access**: Write tools restricted to editor/admin roles
- **Rate limiting**: Configurable per-minute and per-hour limits
- **Input sanitization**: All parameters sanitized using WordPress functions
- **CORS headers**: Configurable allowed origins

## Requirements

- WordPress 6.0+
- PHP 8.0+
- Composer (for dependencies)
- SSL certificate recommended for production

## See Also

- [WordPress wmcp.dev](./wordpress-wmcp-dev.md) — Declarative attribute injection for WordPress forms
- [Wix WebMCP](./wix-webmcp.md) — Wix platform WebMCP extension
- [WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Security & Privacy](https://github.com/webmachinelearning/webmcp/blob/main/docs/security-privacy.md) — Spec security guidance
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [E-commerce use case](../../11-use-cases/e-commerce.md) — E-commerce use case
- [SEO implications](../../11-use-cases/seo-implications.md) — SEO implications
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
- [Agentic web landscape](../../14-industry/agentic-web-landscape.md) — Agentic web landscape
- [Community feature requests](../../13-spec-discussions/community-external-requests.md) — Community feature requests
