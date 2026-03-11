# Architecture — Circle MCP Server

> Design decisions, module structure, and data flow for the Circle MCP Server v0.1.0.

---

## System Overview

The Circle MCP Server is a stdio-based MCP server that translates natural language tool calls from Claude Desktop (or any MCP client) into authenticated Circle Admin API v2 requests. It returns structured, paginated results in both human-readable and machine-parseable formats.

```
Claude Desktop ←→ stdio (JSON-RPC) ←→ MCP Server ←→ HTTPS ←→ Circle API v2
```

The server runs as a local process, communicates over stdin/stdout, and logs diagnostics to stderr.

---

## Module Structure

```
src/
  index.ts              ← Entry point: connects server to stdio transport
  server.ts             ← Server factory: config → client → tools → McpServer
  config/
    env.ts              ← Environment validation with fail-fast semantics
  types/
    circle.ts           ← Circle domain types (Space, Post, Member, SearchResult)
    common.ts           ← Shared types: pagination envelope, response truncation
  lib/
    http.ts             ← HTTP client with auth headers, timeout, retry/backoff
    errors.ts           ← Error normalization → CircleApiError with actionable messages
    responses.ts        ← Shared response builder: text + structuredContent
    pagination.ts       ← Pagination summary extraction from API responses
  schemas/
    inputs.ts           ← Zod input schemas for all 6 tools (strict mode)
  clients/
    circle-client.ts    ← Typed Circle API client (6 typed methods)
  tools/
    index.ts            ← Tool registration orchestrator
    list-spaces.ts      ← circle_list_spaces handler
    get-space.ts        ← circle_get_space handler
    list-posts.ts       ← circle_list_posts handler
    get-post.ts         ← circle_get_post handler
    list-members.ts     ← circle_list_members handler
    search.ts           ← circle_search handler
```

---

## Design Principles

### 1. Layered Separation

Each layer has a single responsibility:

| Layer | Responsibility | Key Files |
|-------|---------------|-----------|
| **Transport** | stdio ↔ JSON-RPC framing | `index.ts` |
| **Server** | Tool registration, lifecycle | `server.ts`, `tools/index.ts` |
| **Tools** | Input validation, response formatting | `tools/*.ts`, `schemas/inputs.ts` |
| **Client** | API method typing, endpoint mapping | `clients/circle-client.ts` |
| **HTTP** | Auth, timeout, retry, error normalization | `lib/http.ts`, `lib/errors.ts` |

No layer reaches across boundaries. Tools never construct HTTP requests directly. The HTTP layer never formats MCP responses.

### 2. Fail-Fast Configuration

The server validates its environment at startup, before accepting any MCP connections:

```
startup → loadEnvConfig() → missing CIRCLE_API_TOKEN? → fatal error (exit 1)
```

The error message is specific and actionable: *"CIRCLE_API_TOKEN is not set. Set it in your environment or .env file."*

This prevents silent failures where the server starts but every tool call fails with 401.

### 3. Strict Input Validation

All tool inputs are validated with Zod schemas using `.strict()` mode:

- Unknown properties are rejected (not silently ignored)
- Types are enforced (integers for IDs, bounded ranges for pagination)
- Required fields are explicitly declared
- Each field includes a `.describe()` annotation for LLM guidance

This prevents malformed requests from reaching the Circle API and produces clear validation errors.

### 4. Dual Response Format

Every successful tool response includes two representations:

```json
{
  "content": [{ "type": "text", "text": "{ ... pretty-printed JSON ... }" }],
  "structuredContent": { "pagination": { ... }, "records": [ ... ] }
}
```

- **`content`** — human-readable text (may be truncated for large payloads)
- **`structuredContent`** — full machine-parseable payload (never truncated)

This ensures both text-based and structured-data MCP clients get optimal results.

### 5. Defensive Error Handling

All errors are normalized through a single pipeline:

```
API error → normalizeApiError() → CircleApiError → formatErrorForMcp()
```

The pipeline maps HTTP status codes to actionable messages:

| Status | Message |
|--------|---------|
| 401 | "Verify your CIRCLE_API_TOKEN is correct and not expired" |
| 403 | "Your API token may lack permissions for this resource" |
| 404 | "Verify the ID exists in your Circle community" |
| 429 | "Circle allows 2,000 requests per 5 minutes per IP. Wait and retry" |
| Timeout | "The Circle API may be slow or unreachable" |

---

## Data Flow

### Successful Tool Call

```
1. Claude Desktop sends JSON-RPC `tools/call` with tool name + params
2. MCP SDK routes to registered tool handler
3. Handler validates input via Zod schema
4. Handler calls CircleClient method
5. CircleClient constructs URL + query params
6. CircleHttpClient sends authenticated GET with timeout
7. Response parsed → typed object returned
8. Handler extracts pagination + records
9. buildToolResponse() formats dual response
10. MCP SDK sends JSON-RPC result back to Claude
```

### Retry Flow

```
1. GET request returns 429/5xx or network error
2. circleHttpClient checks: retries remaining?
3. If 429: read Retry-After header (cap at 60s)
4. Otherwise: exponential delay (1s, 2s)
5. Log retry to stderr
6. Retry the request
7. Max 2 retries (3 total attempts)
```

### Truncation Flow

```
1. buildToolResponse() receives result object
2. JSON.stringify → pretty-printed text
3. prepareSafeText() checks: text > 100,000 chars?
4. If yes: truncate at clean line break, append notice
5. structuredContent remains full (never truncated)
6. Return both in response envelope
```

---

## Tool Annotations

All six tools share consistent MCP annotations:

```json
{
  "readOnlyHint": true,
  "destructiveHint": false,
  "idempotentHint": true,
  "openWorldHint": true
}
```

These signal to the MCP client that:
- Tools only read data (safe to call without confirmation)
- No mutations are performed
- Repeated calls produce the same result
- Results may change with external state (new posts, members)

---

## Testing Strategy

| Test Layer | Count | Scope |
|-----------|-------|-------|
| **Offline smoke tests** | 36 | Unit tests for all shared utilities, error handling, schema validation |
| **Live API tests** | 12 | End-to-end tests against live Circle API (happy + error paths) |

Offline tests run without API credentials and validate all code paths except the HTTP layer. Live tests require a real Circle API token and validate the full request-response cycle.

---

## Technology Choices

| Decision | Choice | Rationale |
|----------|--------|-----------|
| HTTP client | Native `fetch` | Zero dependencies, available since Node 18, sufficient for REST API |
| Schema validation | Zod | First-class TypeScript inference, `.strict()` mode, `.describe()` for LLM hints |
| Transport | stdio | MCP standard for local tool servers, no port management |
| Build | `tsc` only | No bundler needed for a Node.js server, simpler toolchain |
| Test framework | Custom harness | Minimal dependency footprint, structured output, no test runner overhead |
