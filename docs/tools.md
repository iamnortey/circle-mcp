# Tool Reference — Circle MCP Server

> Complete reference for the thirteen read-only tools provided by Circle MCP Server v0.2.0.

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

## Core Read Tools (v0.1.0)

### circle_list_spaces

List spaces in the community with pagination and sorting.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `sort` | enum | No | Sort order |

**Sort options:** `active`, `oldest`, `alphabetical`, `likes`, `latest_updated`, `oldest_updated`, `latest_profile_confirmed`

**Returns:** Pagination envelope + array of space objects with id, name, slug, type, URL, and settings.

---

### circle_get_space

Retrieve a single space by its numeric ID.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `space_id` | integer | **Yes** | Numeric space ID |

**Returns:** Full space object with all fields.

---

### circle_list_posts

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

### circle_get_post

Retrieve a single post by its numeric ID, including the full TipTap rich-text body.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `post_id` | integer | **Yes** | Numeric post ID |

**Returns:** Full post object with complete TipTap body content.

---

### circle_list_members

List community members with status filtering.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |
| `status` | enum | No | Omit for active only, `all` for everyone, `inactive` for inactive |

**Returns:** Pagination envelope + array of member objects with id, name, email, headline, avatar, and joined date.

---

### circle_search

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

## Extended Read Tools (v0.2.0)

### circle_list_comments

List comments on a post with pagination.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `post_id` | integer | No | Filter to a specific post |
| `space_id` | integer | No | Filter to a specific space |
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |

**Returns:** Pagination envelope + array of comment objects with id, body, author, and timestamps.

---

### circle_get_comment

Retrieve a single comment by its numeric ID.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `comment_id` | integer | **Yes** | Numeric comment ID |

**Returns:** Full comment object with all fields.

---

### circle_list_topics

List topics (tags) available in the community.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |

**Returns:** Pagination envelope + array of topic objects with id, name, slug, and post count.

---

### circle_get_community

Retrieve community-level metadata including name, slug, and settings.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| _(none)_ | — | — | No parameters required |

**Returns:** Community object with id, name, slug, URL, and configuration settings.

---

### circle_list_space_groups

List space groups in the community, including the space IDs contained in each group.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `page` | integer | No | Page number |
| `per_page` | integer | No | Results per page |

**Returns:** Pagination envelope + array of space group objects with id, name, and contained space IDs.

---

## Derived Intelligence Tools (v0.2.0)

These tools aggregate multiple API calls to produce analytical summaries. Each response includes a `computation` metadata block showing exactly which API calls were made and how results were derived.

### circle_detect_unanswered_posts

Scan for posts that have zero comments, surfacing unanswered questions in the community.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `space_id` | integer | No | Limit scan to a specific space |
| `per_page` | integer | No | Max posts to scan per page |
| `page` | integer | No | Page to scan |

**Returns:** Object with `unanswered_posts` array, `total_scanned`, `total_unanswered`, and `computation` metadata.

> **Heuristic:** Uses `comments_count === 0` as the unanswered signal. This may not account for deleted comments.

---

### circle_community_health

Generate a point-in-time community health snapshot by aggregating community, space, post, and member data.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| _(none)_ | — | — | No parameters required |

**Returns:** Object with `community` name, `total_spaces`, `total_posts`, `total_members`, `top_spaces_by_posts`, and `computation` metadata.

> **Note:** This is a point-in-time snapshot, not a trend. It shows current counts without historical comparison.

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

### Derived Tool Success

```json
{
  "content": [{ "type": "text", "text": "{ ... }" }],
  "structuredContent": {
    "unanswered_posts": [ ... ],
    "total_scanned": 25,
    "total_unanswered": 3,
    "computation": {
      "calls_made": ["listPosts(space_id=123, per_page=25, page=1)"],
      "heuristic": "comments_count === 0"
    }
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
