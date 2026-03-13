# Live Tool Surface — Source of Truth

> **Captured:** 2026-03-13
> **Source:** `POST /mcp` → `tools/list` on `https://circle-mcp-server-production.up.railway.app`
> **Server:** circle-so-mcp v0.3.0
> **Protocol:** MCP 2025-03-26

---

## Tool Inventory (16 total)

### Read Tools (13)

| Tool | Title | Required Params | Annotations |
|------|-------|-----------------|-------------|
| `circle_list_spaces` | List Circle Spaces | none | readOnly, idempotent |
| `circle_get_space` | Get Circle Space | `space_id` | readOnly, idempotent |
| `circle_list_posts` | List Circle Posts | none | readOnly, idempotent |
| `circle_get_post` | Get Circle Post | `post_id` | readOnly, idempotent |
| `circle_list_members` | List Circle Members | none | readOnly, idempotent |
| `circle_search` | Search Circle Community | `query` | readOnly, idempotent |
| `circle_list_comments` | List Circle Comments | none | readOnly, idempotent |
| `circle_get_comment` | Get Circle Comment | `comment_id` | readOnly, idempotent |
| `circle_list_topics` | List Circle Topics | none | readOnly, idempotent |
| `circle_get_community` | Get Circle Community | none | readOnly, idempotent |
| `circle_list_space_groups` | List Circle Space Groups | none | readOnly, idempotent |
| `circle_detect_unanswered_posts` | Detect Unanswered Posts | none | readOnly, idempotent |
| `circle_community_health` | Community Health Snapshot | none | readOnly, idempotent |

### Write Tools (3)

| Tool | Title | Required Params | Annotations |
|------|-------|-----------------|-------------|
| `circle_create_post` | Create Circle Post | `space_id`, `name`, `body` | non-readOnly, non-idempotent, non-destructive |
| `circle_update_post` | Update Circle Post | `post_id` | non-readOnly, idempotent, non-destructive |
| `circle_create_comment` | Create Circle Comment | `post_id`, `body` | non-readOnly, non-idempotent, non-destructive |

---

## Verification

This tool surface was captured from the live deployed endpoint via JSON-RPC `tools/list` call. It is the canonical reference for what the server exposes. All documentation should match this list exactly.

### Cross-reference check

| Document | Expected Count | Match |
|----------|:-:|:-:|
| README.md | 16 tools (13 read + 3 write) | YES |
| docs/tools.md | 16 tools | YES |
| docs/product/CLIENT_QUICKSTART.md | 13 read + 3 write | YES |
| docs/product/PROMPT_STARTER_PACK.md | references all 16 | YES |
