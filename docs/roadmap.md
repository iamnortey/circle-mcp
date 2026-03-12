# Roadmap — Circle MCP Server

> Planned evolution from read-only community tools to full community management.

---

## Previous: v0.1.0 — Read-Only Foundation

**Status:** Released (2026-03-11)

Six read-only tools covering the core Circle.so data model:

- Spaces (list, get)
- Posts (list with filtering, get with full body)
- Members (list with status filtering)
- Search (cross-community search with type filtering)

Infrastructure:
- Exponential backoff retry for transient failures
- Response truncation guard with structured fallback
- Strict TypeScript with Zod schema validation
- GitHub Actions CI (Node 18, 20, 22)

---

## Previous: v0.2.0 — Community Intelligence

**Status:** Released (2026-03-11)

Seven new read-only tools expanding coverage to comments, topics, community metadata, space groups, and derived analytics. Total: 13 tools.

Extended Read tools:
- `circle_list_comments` — List comments on a post with pagination
- `circle_get_comment` — Retrieve a single comment by ID
- `circle_list_topics` — List topics/tags in the community
- `circle_get_community` — Community-level metadata (name, slug, settings)
- `circle_list_space_groups` — Space groups with contained space IDs

Derived Intelligence tools:
- `circle_detect_unanswered_posts` — Scan for posts with zero comments (multi-call aggregation)
- `circle_community_health` — Point-in-time health snapshot (spaces, posts, members)

Improvements:
- 57 offline tests (21 new), 19 live tests (7 new)
- Derived tools include transparent `computation` metadata
- Zod `.strict()` schemas on all 13 tools
- MCP runtime handshake confirms v0.2.0 and 13-tool listing

---

## Current: v0.3.0 — Safe Content Operations

**Status:** Released (2026-03-11)

Three write tools enabling safe, non-destructive content mutations. Total: 16 tools (13 read + 3 write).

Write tools:
- `circle_create_post` — Create a new post in a space (name, body, space_id required; optional status, slug, comments toggle, notifications, author)
- `circle_update_post` — Update an existing post by ID (post_id required; all other fields optional). Published posts cannot revert to draft
- `circle_create_comment` — Create a comment on a post (post_id, body required; optional author). Known 401 limitation with admin API tokens

Mutation infrastructure:
- `mutate<T>()` method with zero-retry policy (prevents duplicate writes)
- `buildMutationResponse()` envelope for consistent write tool output
- Permission-aware error handling for comment creation (401/403 → actionable workaround)
- Flat endpoint and payload patterns (live-proven against Circle API)

Write tool annotations:
- `readOnlyHint: false`, `destructiveHint: false` on all write tools
- `idempotentHint: true` on update, `idempotentHint: false` on create operations

Improvements:
- 95 offline tests (38 new), 19 live tests
- HTTP request type schemas with Zod validation
- Non-destructive by design — no delete or archive operations

---

## v0.4 — Events, Courses, and Expanded Write Tools

**Scope:** Extend tool coverage to Circle's event and course features, plus additional write operations.

Planned tools:
- `circle_list_events` — List community events
- `circle_get_event` — Get event details
- `circle_list_courses` — List available courses
- `circle_get_course` — Get course details with curriculum
- Additional write tools for member management and content moderation

---

## v0.5 — Webhook Subscriptions

**Scope:** Real-time event streaming via MCP resources.

Planned capabilities:
- MCP resource subscriptions for Circle webhooks
- Notification on new posts, member joins, comments
- Event-driven workflows without polling

---

## Future Considerations

- **Rate-limit tracking** — Cumulative usage monitoring (Circle enforces 2,000 req / 5 min / IP)
- **Cursor pagination** — For communities with deep result sets
- **Streaming responses** — Progressive delivery for large payloads
- **Multi-community support** — Manage multiple Circle communities from a single server
- **npm publication** — Global install via `npx` for simplified distribution
- **File/image upload** — Rich media support for post creation
- **Delete/archive operations** — With confirmation patterns for destructive actions
