# Roadmap — Circle MCP Server

> Planned evolution from read-only community tools to full community management.

---

## Current: v0.1.0 — Read-Only Foundation

**Status:** Released

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

## v0.2 — Write Tools

**Scope:** Create, update, and delete operations for posts and members.

Planned tools:
- `circle_create_post` — Create a new post in a space
- `circle_update_post` — Edit an existing post
- `circle_delete_post` — Remove a post
- `circle_update_member` — Modify member attributes

Design considerations:
- MCP annotations will shift to `readOnlyHint: false`, `destructiveHint: true` for mutations
- Confirmation patterns for destructive operations
- Input validation for rich-text (TipTap) content

---

## v0.3 — Events and Courses

**Scope:** Extend tool coverage to Circle's event and course features.

Planned tools:
- `circle_list_events` — List community events
- `circle_get_event` — Get event details
- `circle_list_courses` — List available courses
- `circle_get_course` — Get course details with curriculum

---

## v0.4 — Webhook Subscriptions

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
