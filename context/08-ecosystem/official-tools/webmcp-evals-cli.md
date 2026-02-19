# WebMCP Evals CLI

> CLI tool for evaluating WebMCP tool quality, description effectiveness, and schema correctness.

## Overview

The **WebMCP Evals CLI** is part of the official [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) repository. It provides automated evaluation of WebMCP tool implementations, checking that tool descriptions are clear and useful for AI agents, schemas are well-formed, and tool behavior matches expectations.

- **Repository**: [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) (under `cli/`)
- **License**: Apache-2.0
- **Runtime**: Node.js 18+

## Key Features

### Description Quality Evaluation

- **Clarity scoring**: Rates tool descriptions on how well they communicate purpose to AI agents
- **Positive framing check**: Ensures descriptions use positive language ("Search flights") not negative ("Don't use for hotels")
- **Completeness analysis**: Verifies descriptions include sufficient context for tool selection
- **Parameter description coverage**: Checks that all parameters have meaningful descriptions

### Schema Validation

- **JSON Schema compliance**: Validates `inputSchema` against JSON Schema draft standards
- **Required field analysis**: Checks that essential parameters are marked as required
- **Type coverage**: Verifies all properties have explicit type declarations
- **Enum and format validation**: Validates proper use of enums, formats, and constraints
- **Output schema checks**: Validates `outputSchema` when defined (spec issue [#9](https://github.com/webmachinelearning/webmcp/issues/9))

### Annotation Verification

- **`readOnlyHint` consistency**: Verifies read-only tools don't perform mutations
- **`destructiveHint` flagging**: Checks that destructive operations are properly annotated
- **`idempotentHint` accuracy**: Validates idempotency claims match tool behavior
- **Side-effect detection**: Warns about tools that may have undeclared side effects

### Execution Testing

- **Smoke tests**: Executes tools with sample inputs to verify basic functionality
- **Error handling**: Tests that tools properly handle invalid inputs
- **Response format**: Validates that tool responses follow the expected content structure
- **Performance benchmarking**: Measures tool execution time

## Installation

```bash
# Install from npm (when published)
npm install -g @googlechromelabs/webmcp-evals

# Or install from source
git clone https://github.com/GoogleChromeLabs/webmcp-tools.git
cd webmcp-tools/cli
npm install
npm link
```

## Usage

### Basic Evaluation

```bash
# Evaluate tools on a live page
webmcp-evals https://your-site.com

# Evaluate a local development server
webmcp-evals http://localhost:3000

# Evaluate with verbose output
webmcp-evals https://your-site.com --verbose
```

### Output Example

```
WebMCP Tool Evaluation Report
═══════════════════════════════

Page: https://flight-search.firebaseapp.com
Tools found: 3

─── searchFlights ───────────────────────────
  Description:  ✅ Clear (score: 9/10)
  Schema:       ✅ Valid JSON Schema
  Parameters:   ✅ All described (3/3)
  Annotations:  ✅ readOnlyHint: true
  Execution:    ✅ Returns valid content
  Performance:  245ms average

─── bookFlight ──────────────────────────────
  Description:  ⚠️  Could be more specific (score: 6/10)
  Schema:       ✅ Valid JSON Schema
  Parameters:   ⚠️  Missing description: seatClass
  Annotations:  ⚠️  Missing destructiveHint
  Execution:    ✅ Returns valid content
  Performance:  890ms average

─── getFlightDetails ────────────────────────
  Description:  ✅ Clear (score: 8/10)
  Schema:       ✅ Valid JSON Schema
  Parameters:   ✅ All described (1/1)
  Annotations:  ✅ readOnlyHint: true
  Execution:    ✅ Returns valid content
  Performance:  120ms average

═══════════════════════════════
Overall Score: 85/100
Recommendations:
  1. Add destructiveHint to bookFlight
  2. Add description for bookFlight.seatClass parameter
  3. Consider adding outputSchema to all tools
```

### CI/CD Integration

```bash
# Exit with non-zero code if score is below threshold
webmcp-evals https://your-site.com --min-score 80

# JSON output for programmatic consumption
webmcp-evals https://your-site.com --format json > eval-results.json

# JUnit XML output for CI systems
webmcp-evals https://your-site.com --format junit > eval-results.xml
```

### Configuration File

Create a `webmcp-evals.config.json` in your project root:

```json
{
  "url": "http://localhost:3000",
  "minScore": 80,
  "timeout": 30000,
  "skipExecution": false,
  "geminiApiKey": "your-key-here",
  "checks": {
    "descriptionQuality": true,
    "schemaValidation": true,
    "annotationVerification": true,
    "executionTesting": true,
    "performanceBenchmark": true
  }
}
```

## CLI Options

| Option | Description | Default |
|--------|-------------|---------|
| `--verbose` | Show detailed output for each check | `false` |
| `--min-score` | Minimum passing score (exit 1 if below) | `0` |
| `--format` | Output format: `text`, `json`, `junit` | `text` |
| `--timeout` | Page load timeout in milliseconds | `30000` |
| `--skip-execution` | Skip tool execution tests | `false` |
| `--config` | Path to configuration file | `webmcp-evals.config.json` |
| `--gemini-key` | Gemini API key for AI-based evaluation | — |

## Evaluation Categories

| Category | Weight | Checks |
|----------|--------|--------|
| **Description Quality** | 30% | Clarity, positive framing, completeness |
| **Schema Correctness** | 25% | JSON Schema validity, types, required fields |
| **Parameter Descriptions** | 15% | Coverage, specificity, format hints |
| **Annotations** | 15% | readOnlyHint, destructiveHint, idempotentHint |
| **Execution** | 15% | Response format, error handling, performance |

## GitHub Actions Example

```yaml
name: WebMCP Evals
on: [push, pull_request]

jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install && npm start &
      - run: npx @googlechromelabs/webmcp-evals http://localhost:3000 --min-score 80
```

## See Also

- [Chrome Labs Toolkit](./chrome-labs-toolkit.md) — Parent project overview
- [Model Context Tool Inspector](./model-context-tool-inspector.md) — Visual tool inspection extension
- [DevTools Quickstart](./devtools-quickstart.md) — Chrome DevTools MCP integration
- [Security Checklist](../../../docs/security-privacy.md) — Tool security best practices
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Execute callback specification](../../02-specification/execute-callback.md) — Execute callback specification
- [Output schema specification](../../02-specification/output-schema.md) — Output schema specification
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Testing tools tutorial](../../12-tutorials/testing-tools.md) — Testing tools tutorial
- [Best practices for WebMCP tools](../../12-tutorials/best-practices.md) — Best practices for WebMCP tools
- [Developer tooling use case](../../11-use-cases/developer-tooling.md) — Developer tooling use case
- [Error handling reference](../../15-reference/error-handling.md) — Error handling reference
- [JSON schema patterns](../../15-reference/json-schema-patterns.md) — JSON schema patterns
