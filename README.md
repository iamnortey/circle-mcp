# Circle MCP Server

> MCP server for the [Circle.so](https://circle.so) Admin API v2 — community intelligence and content management tools for Claude Desktop and compatible MCP clients.

---

## What It Does

Circle MCP Server bridges your Circle.so community with Claude Desktop (or any [Model Context Protocol](https://modelcontextprotocol.io) client). It exposes **sixteen tools** (thirteen read-only + three write) that let an LLM query and manage your community's spaces, posts, members, comments, topics, and search results through natural language — plus derived analytics that aggregate multiple API calls into actionable summaries.

Ask Claude questions like:
- *"What are the most active spaces in my community?"*
- *"Show me recent posts in the Announcements space"*
- *"Find members who joined this month"*
- *"Search for discussions about onboarding"*
- *"Are there any unanswered posts in my community?"*
- *"Give me a health snapshot of my community"*
- *"Create a new post in the Announcements space"*
- *"Update the title of post #12345"*

The server translates these into structured Circle Admin API calls, handles pagination, retries transient failures, and returns clean results.

## Why It Exists

Circle.so is a powerful community platform, but querying it programmatically requires API scripting. The Model Context Protocol (MCP) provides a standardized way for AI assistants to interact with external services through structured tool calls.

This server removes the friction between "I want to know what's happening in my community" and the API calls required to answer that question. It turns your Circle community into a queryable and manageable knowledge source for Claude.

## Architecture

The server follows a clean, layered architecture designed for reliability and maintainability:

```
┌─────────────────────────────────────────────┐
│              Claude Desktop                  │
│         (or any MCP client)                  │
└──────────────┬──────────────────────────────┘
               │ stdio (JSON-RPC)
┌──────────────▼──────────────────────────────┐
│           MCP Server Layer                   │
│  ┌────────────────────────────────────────┐  │
│  │    16 Registered Tool Handlers        │  │
│  │  (validation, response formatting)     │  │
│  └──────────────┬─────────────────────────┘  │
│  ┌──────────────▼─────────────────────────┐  │
│  │         Circle API Client              │  │
│  │  (typed methods, pagination support)   │  │
│  └──────────────┬─────────────────────────┘  │
│  ┌──────────────▼─────────────────────────┐  │
│  │          HTTP Layer                    │  │
│  │  (auth, timeout, retry/backoff,       │  │
│  │   mutation path with zero-retry)       │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
               │ HTTPS
┌──────────────▼──────────────────────────────┐
│         Circle Admin API v2                  │
│        (app.circle.so)                       │
└──────────────────────────────────────────────┘
```

**Key design decisions:**

- **Zero external HTTP dependencies** — uses Node.js native `fetch` (Node 18+)
- **Strict TypeScript** throughout — no `any` types, Zod schema validation on all inputs
- **Fail-fast configuration** — missing API token produces an actionable error immediately
- **Structured + text responses** — every tool returns both human-readable text and machine-parseable `structuredContent`
- **Non-destructive write tools** — create and update operations only, no delete/archive (v0.3.0)

See [ARCHITECTURE.md](./ARCHITECTURE.md) for the full design breakdown.

## Tools

Sixteen tools organized in four tiers:

### Core Read Tools (v0.1.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_list_spaces` | List community spaces | pagination, 7 sort options |
| `circle_get_space` | Get a single space by ID | `space_id` (required) |
| `circle_list_posts` | List posts with filtering | space, status, text search, sort |
| `circle_get_post` | Get a single post with full body | `post_id` (required) |
| `circle_list_members` | List community members | pagination, status filter |
| `circle_search` | Search across the community | `query` (required), type filter |

### Extended Read Tools (v0.2.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_list_comments` | List comments on a post | `post_id`, `space_id`, pagination |
| `circle_get_comment` | Get a single comment by ID | `comment_id` (required) |
| `circle_list_topics` | List topics/tags in the community | pagination |
| `circle_get_community` | Get community metadata | _(no params)_ |
| `circle_list_space_groups` | List space groups with space IDs | pagination |

### Derived Intelligence Tools (v0.2.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_detect_unanswered_posts` | Find posts with zero comments | `space_id` (optional filter) |
| `circle_community_health` | Point-in-time community health snapshot | _(no params)_ |

### Write Tools (v0.3.0)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `circle_create_post` | Create a new post in a space | `space_id`, `name`, `body` (required) |
| `circle_update_post` | Update an existing post by ID | `post_id` (required), all other fields optional |
| `circle_create_comment` | Create a comment on a post | `post_id`, `body` (required) |

Read tools share consistent behavior:
- **Pagination** via `page` and `per_page` parameters
- **Read-only** with MCP annotations (`readOnlyHint: true`, `destructiveHint: false`)
- **Strict input validation** — unknown parameters are rejected
- **Consistent error messages** with actionable guidance

Write tools are non-destructive by design:
- **MCP annotations:** `readOnlyHint: false`, `destructiveHint: false`
- **No delete/archive operations** — only create and update
- **Zero-retry on mutations** — prevents duplicate writes
- **Permission-aware errors** — comment creation 401 returns actionable workaround guidance

See [docs/tools.md](./docs/tools.md) for the complete tool reference.

## Resilience

The server is built for real-world reliability:

| Feature | Behavior |
|---------|----------|
| **Retry with backoff** | Transient failures (429, 5xx) retried up to 2 times with exponential delay |
| **Retry-After support** | Respects Circle's `Retry-After` header on rate-limit responses |
| **Response truncation** | Large payloads capped at 100K characters with structured fallback |
| **Timeout protection** | 30-second request timeout prevents hanging |
| **Actionable errors** | Every error includes specific guidance (e.g., "Verify your API token") |
| **Mutation safety** | Write operations use zero-retry policy to prevent duplicates |
| **Permission-aware handling** | Comment 401 returns structured workaround instead of raw error |

## Technical Stack

| Component | Technology |
|-----------|------------|
| Runtime | Node.js ≥ 18 (native `fetch`) |
| Language | TypeScript (strict mode) |
| Protocol | MCP (Model Context Protocol) v2025-03-26 |
| Transport | stdio (JSON-RPC over stdin/stdout) |
| Validation | Zod with `.strict()` schemas |
| External API | Circle Admin API v2 |
| Testing | 95 offline tests + 19 live API tests |
| CI | GitHub Actions (Node 18/20/22 matrix) |

## Release Status

**Current version: v0.3.0** — Safe Content Operations

This is a stable, validated release supporting 16 tools (13 read + 3 write). The server has been tested against live Circle communities with 95 offline tests and 19 live API tests.

### Roadmap

| Version | Scope |
|---------|-------|
| v0.1.0 | Read-only tools (spaces, posts, members, search) |
| v0.2.0 | Community intelligence (comments, topics, community, space groups, derived analytics) |
| v0.3.0 | Safe content operations (create/update posts, create comments) — **current** |
| v0.4 | Events, courses, and expanded write tools |
| v0.5 | Webhook subscriptions via MCP resources |

## Known Limitations (v0.3)

1. **Comment creation 401** — `circle_create_comment` returns 401 with admin API tokens (a Circle API restriction, not a server bug)
2. **Published post immutability** — published posts cannot be reverted to draft status via `circle_update_post`
3. **Mutation response body empty** — write operations return `body: ""`. Use `circle_get_post` after mutation for full content
4. **No delete/archive operations** — v0.3.0 is non-destructive by design
5. **No rate-limit tracking** — retry/backoff handles individual 429s but doesn't track cumulative usage
6. **Search returns summaries** — use `circle_get_space` or `circle_get_post` for full detail
7. **Page-based pagination** — no cursor pagination support
8. **TipTap body always included** — post listings include full rich-text bodies
9. **No streaming support** — full response payloads only
10. **Single community** — one API token = one community
11. **100 per_page ceiling** — assumed safe maximum (not documented by Circle)
12. **Unanswered heuristic** — `circle_detect_unanswered_posts` uses `comments_count === 0` which may not account for deleted comments
13. **Health snapshot is point-in-time** — `circle_community_health` shows current counts, not trends
14. **No file/image upload** — posts created via API are text/HTML only

## Getting Started

The Circle MCP Server requires:

- **Node.js** ≥ 18.0.0
- **Circle Admin API token** (from Circle Admin → Settings → API)
- **An MCP client** (Claude Desktop, MCP Inspector, or compatible client)

The server runs over stdio and is configured in your MCP client's server configuration file. Refer to the source repository's operator documentation for detailed setup instructions.

## License

MIT
