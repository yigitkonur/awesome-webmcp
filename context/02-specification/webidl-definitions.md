# WebIDL Definitions — Complete Interface Specification

## Overview

This document contains the complete [WebIDL](https://webidl.spec.whatwg.org/) definitions from the WebMCP [Bikeshed
specification](https://github.com/webmachinelearning/webmcp/blob/main/index.bs) (`index.bs`). WebIDL (Web Interface Definition Language) is the standard
way to describe web platform APIs and is used by all W3C specifications.
The current WebIDL was introduced in [PR #75](https://github.com/webmachinelearning/webmcp/pull/75).

## Complete WebIDL

### Navigator Extension

```webidl
partial interface Navigator {
  [SecureContext, SameObject] readonly attribute ModelContext modelContext;
};
```

Adds a single property to the existing `Navigator` interface
(see [spec](https://webmachinelearning.github.io/webmcp)):

| Attribute | Extended Attributes | Type | Description |
|---|---|---|---|
| `modelContext` | `[SecureContext, SameObject] readonly` | `ModelContext` | Entry point to WebMCP API |

### ModelContext Interface

```webidl
[Exposed=Window, SecureContext]
interface ModelContext {
  undefined provideContext(optional ModelContextOptions options = {});
  undefined clearContext();
  undefined registerTool(ModelContextTool tool);
  undefined unregisterTool(DOMString name);
};
```

Extended attributes:
- `[Exposed=Window]` — Available only in window contexts (not workers, unless extended)
- `[SecureContext]` — Requires HTTPS

| Method | Parameters | Return | Description |
|---|---|---|---|
| `provideContext()` | `optional ModelContextOptions options = {}` | `undefined` | Register tools, clearing existing set |
| `clearContext()` | (none) | `undefined` | Remove all registered tools |
| `registerTool()` | `ModelContextTool tool` | `undefined` | Add a single tool (additive) |
| `unregisterTool()` | `DOMString name` | `undefined` | Remove a tool by name |

### ModelContextOptions Dictionary

```webidl
dictionary ModelContextOptions {
  sequence<ModelContextTool> tools = [];
};
```

| Member | Type | Default | Description |
|---|---|---|---|
| `tools` | `sequence<ModelContextTool>` | `[]` | List of tools to register |

### ModelContextTool Dictionary

```webidl
dictionary ModelContextTool {
  required DOMString name;
  required DOMString description;
  object inputSchema;
  required ToolExecuteCallback execute;
  ToolAnnotations annotations;
};
```

| Member | Type | Required | Description |
|---|---|---|---|
| `name` | `DOMString` | ✅ | Unique tool identifier |
| `description` | `DOMString` | ✅ | Natural language description |
| `inputSchema` | `object` | ❌ | JSON Schema for input parameters |
| `execute` | `ToolExecuteCallback` | ✅ | Callback invoked on tool call |
| `annotations` | `ToolAnnotations` | ❌ | Behavioral metadata hints |

### ToolAnnotations Dictionary

```webidl
dictionary ToolAnnotations {
  boolean readOnlyHint;
};
```

| Member | Type | Required | Description |
|---|---|---|---|
| `readOnlyHint` | `boolean` | ❌ | If true, tool only reads data |

### ToolExecuteCallback

```webidl
callback ToolExecuteCallback = Promise<any> (object input, ModelContextClient client);
```

| Parameter | Type | Description |
|---|---|---|
| `input` | `object` | Input parameters matching `inputSchema` |
| `client` | `ModelContextClient` | Interface to the calling agent |
| **returns** | `Promise<any>` | Tool result (typically content array) |

### ModelContextClient Interface

```webidl
[Exposed=Window, SecureContext]
interface ModelContextClient {
  Promise<any> requestUserInteraction(UserInteractionCallback callback);
};
```

Extended attributes:
- `[Exposed=Window]` — Available only in window contexts
- `[SecureContext]` — Requires HTTPS

| Method | Parameters | Return | Description |
|---|---|---|---|
| `requestUserInteraction()` | `UserInteractionCallback callback` | `Promise<any>` | Request user input during tool execution |

### UserInteractionCallback

```webidl
callback UserInteractionCallback = Promise<any> ();
```

| Parameter | Type | Description |
|---|---|---|
| (none) | — | — |
| **returns** | `Promise<any>` | Result of user interaction |

## Interface Hierarchy

```
Navigator (partial)
  └── modelContext: ModelContext
        ├── provideContext(options?: ModelContextOptions)
        │     └── ModelContextOptions
        │           └── tools: sequence<ModelContextTool>
        │                 └── ModelContextTool
        │                       ├── name: DOMString
        │                       ├── description: DOMString
        │                       ├── inputSchema?: object
        │                       ├── execute: ToolExecuteCallback
        │                       │     └── (input, client) => Promise<any>
        │                       │           └── client: ModelContextClient
        │                       │                 └── requestUserInteraction(cb)
        │                       │                       └── cb: UserInteractionCallback
        │                       └── annotations?: ToolAnnotations
        │                             └── readOnlyHint?: boolean
        ├── clearContext()
        ├── registerTool(tool: ModelContextTool)
        └── unregisterTool(name: DOMString)
```

## Extended Attributes Explained

### [SecureContext]

```webidl
[SecureContext, SameObject] readonly attribute ModelContext modelContext;
```

The `[SecureContext]` extended attribute means the member is only available when the
script is running in a [secure context](https://w3c.github.io/webappsec-secure-contexts/).
In practice, this means HTTPS or localhost.

### [SameObject]

```webidl
[SecureContext, SameObject] readonly attribute ModelContext modelContext;
```

The `[SameObject]` extended attribute guarantees that the getter always returns the
same JavaScript object:

```javascript
navigator.modelContext === navigator.modelContext // always true
```

### [Exposed=Window]

```webidl
[Exposed=Window, SecureContext]
interface ModelContext { ... };
```

The `[Exposed=Window]` extended attribute means the interface is available in
`Window` contexts (regular web pages). It is **not** available in:
- `DedicatedWorkerGlobalScope`
- `SharedWorkerGlobalScope`
- `ServiceWorkerGlobalScope`

The service worker extension [proposal](https://github.com/webmachinelearning/webmcp/blob/main/docs/service-workers.md) may add `[Exposed=ServiceWorker]` in the future.

## Algorithm Stubs

The Bikeshed spec includes algorithm stubs for each method. These are marked as TODO
in the current draft:

### provideContext Algorithm

```
The provideContext(options) method steps are:
1. TODO: fill this out.
```

### clearContext Algorithm

```
The clearContext() method steps are:
1. TODO: fill this out.
```

### registerTool Algorithm

```
The registerTool(tool) method steps are:
1. TODO: fill this out.
```

### unregisterTool Algorithm

```
The unregisterTool(name) method steps are:
1. TODO: fill this out.
```

### requestUserInteraction Algorithm

```
The requestUserInteraction(callback) method steps are:
1. TODO: fill this out.
```

These algorithms will be fully specified as the [spec](https://webmachinelearning.github.io/webmcp) matures.

## Normative References in WebIDL

The WebIDL references these types from other specifications
(see [W3C WebIDL spec](https://webidl.spec.whatwg.org/)):

| Type | Source | Description |
|---|---|---|
| `DOMString` | Web IDL | UTF-16 string |
| `object` | Web IDL | Any JavaScript object |
| `boolean` | Web IDL | true or false |
| `undefined` | Web IDL | The undefined value |
| `sequence<T>` | Web IDL | Ordered list (maps to Array in JS) |
| `Promise<T>` | Web IDL | JavaScript Promise |
| `Navigator` | HTML | The navigator object |

## TypeScript Equivalent

For TypeScript developers, here's the equivalent interface definitions:

```typescript
interface Navigator {
  readonly modelContext: ModelContext;
}

interface ModelContext {
  provideContext(options?: ModelContextOptions): void;
  clearContext(): void;
  registerTool(tool: ModelContextTool): void;
  unregisterTool(name: string): void;
}

interface ModelContextOptions {
  tools?: ModelContextTool[];
}

interface ModelContextTool {
  name: string;
  description: string;
  inputSchema?: Record<string, any>;
  execute: (input: Record<string, any>, client: ModelContextClient) => Promise<any>;
  annotations?: ToolAnnotations;
}

interface ToolAnnotations {
  readOnlyHint?: boolean;
}

interface ModelContextClient {
  requestUserInteraction(callback: () => Promise<any>): Promise<any>;
}
```

## Bibliographic References

From the spec's `<pre class="biblio">` block:

```json
{
  "mcp": {
    "href": "https://modelcontextprotocol.io/specification/latest",
    "title": "Model Context Protocol (MCP) Specification",
    "publisher": "The Linux Foundation"
  },
  "json-schema": {
    "href": "https://json-schema.org/draft/2020-12/json-schema-core.html",
    "title": "JSON Schema: A Media Type for Describing JSON Documents",
    "publisher": "JSON Schema"
  }
}
```

## Related Issues

- [PR #75 — WebIDL definitions](https://github.com/webmachinelearning/webmcp/pull/75) — Formal WebIDL interface definitions
- [Issue #9 — Should output also have a schema?](https://github.com/webmachinelearning/webmcp/issues/9) — Potential `outputSchema` addition
- [Issue #48 — Abort callback](https://github.com/webmachinelearning/webmcp/issues/48) — Potential `AbortSignal` addition
- [Issue #53 — Semantic hints](https://github.com/webmachinelearning/webmcp/issues/53) — ToolAnnotations expansion

## See Also

- [Specification overview](spec-overview.md)
- [navigator.modelContext API](navigator-model-context.md)
- [Tool registration interface](tool-registration-interface.md)
- [registerTool() method](register-tool.md)
- [inputSchema for tool inputs](input-schema.md)
- [outputSchema for structured outputs](output-schema.md)
- [Execute callback specification](execute-callback.md)
- [What is WebMCP](../01-overview/what-is-webmcp.md)
- [Glossary of terms](../01-overview/glossary.md)
- [WebIDL specification reference](../15-reference/webidl-spec.md)
- [API reference](../15-reference/api-reference.md)
