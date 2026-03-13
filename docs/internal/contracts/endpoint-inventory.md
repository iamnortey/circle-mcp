# Circle MCP v0.1 — Endpoint Inventory

> Generated: 2026-03-11
> Source: `artifacts/openapi/circle-admin-v2-swagger.yaml` (OpenAPI 3.0.1, 543 KB)
> Base URL: `https://app.circle.so`

---

## Authentication

| Field | Value |
|-------|-------|
| Scheme | `token_auth` (apiKey) |
| Header | `Authorization: Token ***REDACTED***` |
| Additional | `host` header listed in spec but NOT required in practice (tested without it) |

---

## Pagination Contract (Universal)

All list endpoints share this response envelope:

```json
{
  "page": 1,
  "per_page": 10,
  "has_next_page": true,
  "count": 18,
  "page_count": 5,
  "records": [...]
}
```

- Default `per_page`: 10 (most endpoints)
- Max `per_page`: not documented in spec; needs runtime testing
- Cursor-based pagination: NOT used — offset/page only

---

## Tool → Endpoint Mapping

### 1. `circle_list_spaces`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/spaces` |
| Spec Location | Line 14499 |
| Confirmed Live | ✅ 200 OK — 18 spaces, 5 returned (`per_page=5`) |

**Query Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `page` | integer | No | Default: 1 |
| `per_page` | integer | No | Default: 10 |
| `sort` | enum | No | `active`, `oldest`, `alphabetical`, `likes`, `latest_updated`, `oldest_updated`, `latest_profile_confirmed` |

**Response Record Fields (confirmed live):**
`id`, `name`, `slug`, `cover_image_url`, `cover_image_visible`, `cover_image_display_style`,
`community_id`, `is_private`, `is_hidden_from_non_members`, `is_hidden`, `hide_post_settings`,
`display_view`, `is_post_disabled`, `space_type`, `host`, `post_ids[]`, `url`,
`custom_emoji_url`, `custom_emoji_dark_url`, `emoji`, `thumbnail_image_url`,
`space_group{id, name}`, `topics[]`, `event_auto_rsvp_enabled`,
`default_*_notification_setting` (×6), `default_sort`, `default_comment_sort`,
`default_member_sort`, `default_tab`, `show_tab_bar`, `show_next_event`,
`hide_from_sidebar`, `hide_right_sidebar`, `visible_tabs{}`,
`locked_button_url`, `locked_button_label`, `locked_page_heading`, `locked_page_description`,
`hide_sorting`, `require_topic_selection`, `prevent_members_from_adding_others`,
`hide_from_featured_areas`, `disable_member_post_covers`, `hide_members_count`,
`pinned_posts_label`, `show_lock_icon_for_non_members`, `lock_screen_blocks`

---

### 2. `circle_get_space`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/spaces/{id}` |
| Spec Location | Line 14681 |
| Confirmed Live | ✅ 200 OK — Space ID 2523783 ("Start Here") |

**Path Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `id` | integer | Yes | Space ID |

**Response:** Single space object (same fields as list record). Not wrapped in pagination envelope.

---

### 3. `circle_list_posts`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/posts` |
| Spec Location | Line 12011 |
| Confirmed Live | ✅ 200 OK — 7 posts, 3 returned (`per_page=3`) |

**Query Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `page` | integer | No | Default: 1 |
| `per_page` | integer | No | Default: 10 |
| `space_id` | integer | No | Filter by space |
| `space_group_id` | integer | No | Filter by space group |
| `status` | enum | No | `draft`, `published`, `scheduled`, `all` |
| `search_text` | string | No | Text search within posts |
| `sort` | enum | No | `oldest`, `latest`, `alphabetical`, `likes`, `latest_updated`, `oldest_updated` |

**Response Record Fields (confirmed live):**
`id`, `status`, `name`, `slug`, `comments_count`, `hide_meta_info`, `published_at`,
`created_at`, `updated_at`, `is_comments_enabled`, `is_liking_enabled`,
`flagged_for_approval_at`, `body{id, name, body, record_type, record_id}`,
`tiptap_body{body{type, content[]}, circle_ios_fallback_text, attachments[],
inline_attachments[], sgids_to_object_map, format, community_members[], entities[],
group_mentions[], polls[]}`, `url`, `space_name`, `space_slug`, `space_id`,
`user_id`, `user_email`, `user_name`, `community_id`, `user_avatar_url`,
`cover_image_url`, `cover_image`, `cardview_thumbnail_url`, `cardview_thumbnail`,
`is_comments_closed`, `custom_html`, `likes_count`, `member_posts_count`,
`member_comments_count`, `member_likes_count`, `topics[]`

**Key Finding:** List response DOES include `tiptap_body` for each record (confirmed live, contrary to spec uncertainty).

---

### 4. `circle_get_post`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/posts/{id}` |
| Spec Location | Line 12221 |
| Confirmed Live | ✅ 200 OK — Post ID 30503041 ("Your AI journey starts here") |

**Path Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `id` | integer | Yes | Post ID |

**Response:** Single post object (same fields as list record including full `tiptap_body`). Not wrapped in pagination envelope.

**TipTap Body Structure (confirmed live):**
```
tiptap_body:
  body:
    type: "doc"
    content: [{type: "paragraph", content: [{type: "text", text: "..."}]}, ...]
  circle_ios_fallback_text: string
  attachments: []
  inline_attachments: []
  sgids_to_object_map: {}
  format: string
  community_members: []
  entities: []
  group_mentions: []
  polls: []
```

---

### 5. `circle_list_members`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/community_members` |
| Spec Location | Line 5301 |
| Confirmed Live | ✅ 200 OK — 1 member returned |

**Query Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `page` | integer | No | Default: 1 |
| `per_page` | integer | No | Default: 10 |
| `status` | enum | No | `all`, `inactive` (default returns active members) |

**Response Record Fields (confirmed live):**
`id`, `first_name`, `last_name`, `name`, `email`, `headline`, `avatar_url`,
`profile_url`, `public_uid`, `user_id`, `community_id`,
`created_at`, `updated_at`, `last_seen_at`, `profile_confirmed_at`,
`accepted_invitation`, `active`, `sso_provider_user_id`,
`posts_count`, `comments_count`,
`member_tags[]`, `profile_fields[]`, `flattened_profile_fields{}`,
`gamification_stats{}` (spec only — not confirmed in sample)

**Note:** Only 1 member in community. `activity_score` mentioned in spec but not observed in live response.

---

### 6. `circle_search`

| Field | Value |
|-------|-------|
| Method | `GET` |
| Path | `/api/admin/v2/advanced_search` |
| Spec Location | Line 3732 |
| Confirmed Live | ✅ 200 OK — query="AI" returned 4 results |

**Query Parameters:**

| Param | Type | Required | Notes |
|-------|------|----------|-------|
| `page` | integer | No | Default: 1 |
| `per_page` | integer | No | Default: 10 |
| `query` | string | **Yes** | Search term (REQUIRED) |
| `type` | enum | No | `general`, `members`, `posts`, `comments`, `spaces`, `lessons`, `events`, `entity_list`, `mentions` |
| `mention_scope` | enum | No | `space`, `group_chat`, `thread`, `direct` |
| `filters` | object | No | deepObject: `space_ids[]`, `topic_ids[]`, `member_tag_ids[]`, `author_name`, `status` |

**Response Record Fields (confirmed live — search returns mixed types):**
`id`, `name`, `slug`, `type`, `sgid`, `highlighted_name`,
`emoji`, `custom_emoji_url`, `custom_emoji_dark_url`, `space_type`

**Key Finding:** Search records are lightweight summary objects — NOT full space/post objects. The `type` field distinguishes result types. For full details, use the corresponding detail endpoint.

---

## Spec vs. Live Discrepancies

| Item | Spec Says | Live Confirms | Impact |
|------|-----------|---------------|--------|
| `host` header | Required (apiKey) | NOT required (calls succeed without it) | Low — can omit |
| `tiptap_body` in list posts | Uncertain (spec example partial) | Present in all list records | Positive — richer list data |
| `activity_score` in members | Present in schema | Not observed in live response | Low — may need more members |
| `gamification_stats` in members | Present in schema | Present but may be empty `{}` | Low |
| Search record shape | Mixed-type with full objects | Lightweight summary objects | Important — search returns summaries only |
| Base URL | `api-headless.circle.so` (spec host) | `app.circle.so` (works) | Use `app.circle.so` for API calls |

---

## Rate Limits (from documentation)

| Plan | Requests / 5 min / IP | Monthly Quota |
|------|----------------------|---------------|
| Business | 2,000 | 5,000 |
| Enterprise | 2,000 | 30,000 |
| Plus Platform | 2,000 | 250,000 |

No rate-limit headers observed in sample responses. Implementation should include defensive retry logic.
