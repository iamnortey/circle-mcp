# Tool Reference — Circle MCP Server

> Complete reference for the six read-only tools provided by Circle MCP Server v0.1.0.

---

## Overview

All tools follow consistent patterns:

- **Naming:** `circle_` prefix with `snake_case`
- **Annotations:** `readOnlyHint: true`, `destructiveHint: false`, `idempotentHint: true`
- **Validation:** Zod `.strict()` schemas — unknown parameters are rejected
- **Responses:** Dual format (human-readable text + machine-parseable `structuredContent`)
- **Errors:** Actionable messages with specific guidance

---

## Shared Parameters

These parameters are available on all list endpoints:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | integer (≥1) | 1 | Page number for pagination |
| `per_page` | integer (1–100) | 10 | Results per page |

---

## circle_list_spaces

List spaces in the community with pagination and sorting.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `sort` | enum | No | Sort order |

**Sort options:** `active`, `oldest`, `alphabetical`, `likes`, `latest_updated`, `oldest_updated`, `latest_profile_confirmed`

**Returns:** Pagination envelope + array of space objects with id, name, slug, type, URL, and settings.

---

## circle_get_space

Retrieve a single space by its numeric ID.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `space_id` | integer | **Yes** | Numeric space ID |

**Returns:** Full space object with all fields.

---

## circle_list_posts

List posts with filtering by space, status, and text search.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `space_id` | integer | No | Filter to specific space |
| `space_group_id` | integer | No | Filter to specific space group |
| `status` | enum | No | `draft`, `published`, `scheduled`, `all` |
| `search_text` | string | No | Text search within content |
| `sort` | enum | No | Sort order |

**Sort options:** `oldest`, `latest`, `alphabetical`, `likes`, `latest_updated`, `oldest_updated`

**Returns:** Pagination envelope + array of post objects with title, body (HTML + TipTap), author, and engagement metrics.

> Posts include the full `tiptap_body` which can be large. Use smaller `per_page` values for post-heavy queries.

---

## circle_get_post

Retrieve a single post by its numeric ID, including the full TipTap rich-text body.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `post_id` | integer | **Yes** | Numeric post ID |

**Returns:** Full post object with complete TipTap body content.

---

## circle_list_members

List community members with status filtering.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `status` | enum | No | Omit for active only, `all` for everyone, `inactive` for inactive |

**Returns:** Pagination envelope + array of member objects with id, name, email, headline, avatar, and joined date.

---

## circle_search

Search across the community. Returns lightweight summary objects — use `circle_get_space` or `circle_get_post` for full details on specific results.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | **Yes** | Search query (minimum 1 character) |
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `type` | enum | No | Filter by result type |

**Type options:** `general`, `members`, `posts`, `comments`, `spaces`, `lessons`, `events`, `entity_list`, `mentions`

**Returns:** Pagination envelope + array of lightweight result objects with id, name, slug, and type. These are summary objects, not full resources.

---

## Response Format

### Success

```json
{
  "content": [{ "type": "text", "text": "{ ... }" }],
  "structuredContent": {
    "pagination": {
      "page": 1,
      "per_page": 10,
      "has_next_page": true,
      "count": 42,
      "page_count": 5
    },
    "records": [ ... ]
  }
}
```

### Error

```json
{
  "isError": true,
  "content": [{ "type": "text", "text": "Authentication failed for GET /api/admin/v2/spaces (401). Verify your CIRCLE_API_TOKEN is correct." }]
}
```

---

## Error Reference

| Status | Meaning | Guidance |
|--------|---------|----------|
| 401 | Authentication failed | Verify API token is correct and not expired |
| 403 | Permission denied | Ensure token has Admin-level access |
| 404 | Resource not found | Verify the space/post/member ID exists |
| 422 | Validation failed | Check parameters against the schema above |
| 429 | Rate limited | Circle allows 2,000 req / 5 min / IP — wait and retry |
| Timeout | Request timed out | Circle API may be slow — retries handle transient failures |
