# Sample Capture Notes

> Captured: 2026-03-11
> Community ID: 495139 (learn.ninobyte.com)
> API Base: `https://app.circle.so`
> Auth: `Authorization: Token ***REDACTED***`

---

## Capture Method

All samples captured via `curl` from macOS terminal using credentials stored in
`app/circle-mcp-server/.env` (gitignored). Token read via `grep` to avoid exposing
in shell history beyond the curl process.

---

## Curl Patterns Used (Token Redacted)

```bash
# List spaces
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/spaces?per_page=5"

# Get space detail
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/spaces/2523783"

# List posts
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/posts?per_page=3"

# Get post detail
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/posts/30503041"

# List members
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/community_members?per_page=3"

# Advanced search
curl -s -H "Authorization: Token ***REDACTED***" \
  "https://app.circle.so/api/admin/v2/advanced_search?query=AI&per_page=3"
```

---

## Captured Files

| File | Endpoint | HTTP | Size | Records |
|------|----------|------|------|---------|
| `spaces.json` | GET /spaces?per_page=5 | 200 | 11,969 B | 5 of 18 |
| `space_detail.json` | GET /spaces/2523783 | 200 | 2,038 B | 1 (single) |
| `posts.json` | GET /posts?per_page=3 | 200 | 51,498 B | 3 of 7 |
| `post_detail.json` | GET /posts/30503041 | 200 | 19,351 B | 1 (single) |
| `members.json` | GET /community_members?per_page=3 | 200 | 22,072 B | 1 of 1 |
| `search.json` | GET /advanced_search?query=AI&per_page=3 | 200 | 1,936 B | 3 of 4 |

**Total: 6 files, 108,864 bytes, all 200 OK**

---

## IDs Used for Detail Calls

| Resource | ID | Name | Source |
|----------|-----|------|--------|
| Space | 2523783 | "Start Here" | First record from spaces.json |
| Post | 30503041 | "Your AI journey starts here" | First record from posts.json |

---

## Observations

### Auth
- `Authorization: Token {token}` works without `host` header
- Base URL `https://app.circle.so` confirmed working
- `https://api-headless.circle.so` returns non-JSON (likely HTML redirect)

### Data Volume
- Community has 18 spaces, 7 posts, 1 member (small/new community)
- Posts contain rich TipTap JSON body (can be large — 19KB for single post detail)
- Search for "test" returned 0 results; "AI" returned 4 matches

### Schema Notes
- Search returns lightweight summary objects (id, name, slug, type, sgid) — NOT full objects
- List posts includes `tiptap_body` in all records (spec was ambiguous on this)
- Members response includes `gamification_stats` but may be empty object
- `activity_score` field mentioned in spec but NOT present in live response
- Detail endpoints return unwrapped single objects (no pagination envelope)

### Blockers
- None. All 6 endpoints responded successfully.

### PII Warning
- `members.json` contains real email addresses and profile data
- `posts.json` and `post_detail.json` may contain user-generated content
- These files are gitignored via root `.gitignore` pattern on `artifacts/samples/`
- DO NOT commit sample files to version control
