# Prompt 2/6 — Contract Discovery Report

> Date: 2026-03-11
> Phase: API Contract Discovery, Spec Capture, and Sample Inventory
> Community: 495139 (learn.ninobyte.com)
> Status: **COMPLETE — GREEN**

---

## 1. OpenAPI Spec Status

| Item | Status |
|------|--------|
| Source | `https://api-headless.circle.so/api/admin/v2/swagger.yaml` |
| Format | OpenAPI 3.0.1 (YAML only — JSON variant returns 404) |
| Size | 543 KB |
| Saved to | `artifacts/openapi/circle-admin-v2-swagger.yaml` |
| Validation | Parsed successfully, ~70+ endpoints catalogued |
| Title | "Admin API V2" |
| Coverage | All 6 v0.1 endpoints found and documented |

**Verdict:** Spec is authoritative and complete for v0.1 scope.

---

## 2. Endpoint Inventory Summary

All 6 locked v0.1 tools have been mapped to live, confirmed endpoints:

| # | MCP Tool | HTTP Method | Endpoint Path | Live Status |
|---|----------|-------------|---------------|-------------|
| 1 | `circle_list_spaces` | GET | `/api/admin/v2/spaces` | ✅ 200 OK |
| 2 | `circle_get_space` | GET | `/api/admin/v2/spaces/{id}` | ✅ 200 OK |
| 3 | `circle_list_posts` | GET | `/api/admin/v2/posts` | ✅ 200 OK |
| 4 | `circle_get_post` | GET | `/api/admin/v2/posts/{id}` | ✅ 200 OK |
| 5 | `circle_list_members` | GET | `/api/admin/v2/community_members` | ✅ 200 OK |
| 6 | `circle_search` | GET | `/api/admin/v2/advanced_search` | ✅ 200 OK |

**Full inventory:** `docs/contracts/endpoint-inventory.md`

---

## 3. Sample Capture Summary

| File | Endpoint | Size | Records |
|------|----------|------|---------|
| `artifacts/samples/spaces.json` | List Spaces | 11,969 B | 5 of 18 |
| `artifacts/samples/space_detail.json` | Show Space | 2,038 B | 1 |
| `artifacts/samples/posts.json` | List Posts | 51,498 B | 3 of 7 |
| `artifacts/samples/post_detail.json` | Show Post | 19,351 B | 1 |
| `artifacts/samples/members.json` | List Members | 22,072 B | 1 of 1 |
| `artifacts/samples/search.json` | Advanced Search | 1,936 B | 3 of 4 |

**6/6 samples captured. 0 missing. 0 failures.**

**Capture notes:** `artifacts/notes/sample-capture-notes.md`

---

## 4. Confidence Levels

| Area | Confidence | Notes |
|------|-----------|-------|
| Auth mechanism | **HIGH** | `Authorization: Token {token}` confirmed. `host` header NOT required despite spec listing it. |
| Base URL | **HIGH** | `https://app.circle.so` confirmed working. `api-headless.circle.so` is spec-hosting only. |
| Pagination contract | **HIGH** | Consistent `{page, per_page, has_next_page, count, page_count, records[]}` across all list endpoints. |
| Space schema | **HIGH** | Spec and live response align closely. All fields documented. |
| Post schema | **HIGH** | Full `tiptap_body` present in both list and detail responses. Rich content model well-understood. |
| Member schema | **MEDIUM** | Only 1 member in community. `activity_score` in spec not observed live. `gamification_stats` present but empty. Need more members for full validation. |
| Search schema | **MEDIUM** | Returns lightweight summary objects, NOT full resource objects. Type-specific field sets not fully explored (only space-type results observed). |
| Rate limits | **LOW** | Documented as 2,000/5min/IP but no response headers observed. No retry-after behavior tested. |
| Error responses | **LOW** | Only success responses captured. Error shapes (401, 404, 422, 429) not yet documented. |
| Max `per_page` | **LOW** | Not documented in spec. Not tested beyond default values. |

---

## 5. Schema Uncertainties

### 5.1 Resolved Uncertainties
- **Q:** Does list posts include `tiptap_body`? → **YES**, confirmed in all list records
- **Q:** Is `host` header required? → **NO**, calls succeed without it
- **Q:** Which base URL to use? → `https://app.circle.so` (not `api-headless.circle.so`)

### 5.2 Remaining Uncertainties
- **Member `activity_score`:** Listed in spec, not present in live response. May appear with more active members or require a different API plan.
- **Search result types:** Only `space`-type results observed. Need to test `type=posts`, `type=members`, etc. to see full polymorphic shapes.
- **Search `filters` encoding:** Spec uses `deepObject` style with `explode=true`. Not tested with real filter values.
- **Error response shapes:** Need to capture 401 (bad token), 404 (bad ID), 422 (bad params), and 429 (rate limit) responses.
- **`per_page` max:** Undocumented ceiling — should test with `per_page=100` during implementation.
- **Post body HTML vs TipTap:** The `body.body` field contains rendered HTML. The `tiptap_body.body` contains structured JSON. MCP tools should expose both or choose one.

---

## 6. Auth & Security Findings

| Finding | Detail |
|---------|--------|
| Token format | `Token {32-char-alphanumeric}` |
| Token storage | `app/circle-mcp-server/.env` (gitignored at both root and app level) |
| `.env.example` | Created with `***REDACTED***` placeholders |
| Risk: token in shell history | Mitigated by reading from .env via grep, not typing directly |
| Risk: PII in samples | Members contain emails; samples are gitignored |
| Risk: token rotation | Not tested; assume token has no expiry until confirmed |

---

## 7. Data Model Insights (for Prompt 3/6)

### Pagination (implement once, reuse everywhere)
```typescript
interface CirclePaginatedResponse<T> {
  page: number;
  per_page: number;
  has_next_page: boolean;
  count: number;
  page_count: number;
  records: T[];
}
```

### Key Type Shapes (minimal — detail in endpoint-inventory.md)
- **Space:** `{id: number, name: string, slug: string, space_type: string, community_id: number, ...}`
- **Post:** `{id: number, name: string, slug: string, status: string, tiptap_body: TipTapBody, ...}`
- **Member:** `{id: number, name: string, email: string, first_name: string, last_name: string, ...}`
- **SearchResult:** `{id: number, name: string, slug: string, type: string, sgid: string, ...}` (lightweight)

### TipTap Body (most complex nested structure)
```typescript
interface TipTapBody {
  body: { type: "doc"; content: TipTapNode[] };
  circle_ios_fallback_text: string;
  attachments: unknown[];
  inline_attachments: unknown[];
  sgids_to_object_map: Record<string, unknown>;
  format: string;
  community_members: unknown[];
  entities: unknown[];
  group_mentions: unknown[];
  polls: unknown[];
}
```

---

## 8. Readiness Verdict for Prompt 3/6

### ✅ GO — Ready to proceed to Prompt 3/6 (TypeScript Scaffolding)

**Justification:**

| Prerequisite | Status |
|-------------|--------|
| OpenAPI spec captured | ✅ Complete |
| All 6 endpoints confirmed live | ✅ 6/6 — 200 OK |
| Response samples saved | ✅ 6/6 files |
| Auth mechanism proven | ✅ Token header works |
| Pagination contract understood | ✅ Consistent across endpoints |
| Base URL confirmed | ✅ `app.circle.so` |
| Secret handling in place | ✅ `.env` gitignored, `.env.example` created |
| No blocking unknowns | ✅ All critical schemas documented |

**Pre-conditions for Prompt 3/6:**
1. Use `app.circle.so` as base URL (not `api-headless.circle.so`)
2. Auth header format: `Authorization: Token {token}`
3. No `host` header needed
4. Pagination is page-based, not cursor-based
5. Search requires `query` param (non-optional)
6. Search returns summary objects — may need follow-up detail calls
7. Error handling should be designed defensively (error shapes not yet captured)

---

## 9. File Manifest

```
~/Developer/circle-mcp/
├── artifacts/
│   ├── openapi/
│   │   └── circle-admin-v2-swagger.yaml    # 543 KB OpenAPI 3.0.1 spec
│   ├── samples/
│   │   ├── spaces.json                      # List spaces (5 records)
│   │   ├── space_detail.json                # Single space (ID 2523783)
│   │   ├── posts.json                       # List posts (3 records)
│   │   ├── post_detail.json                 # Single post (ID 30503041)
│   │   ├── members.json                     # List members (1 record)
│   │   └── search.json                      # Search "AI" (3 results)
│   └── notes/
│       └── sample-capture-notes.md          # Curl patterns & observations
├── docs/
│   └── contracts/
│       ├── endpoint-inventory.md            # Full v0.1 endpoint mapping
│       └── prompt-2-contract-discovery.md   # THIS FILE
└── app/
    └── circle-mcp-server/
        ├── .env                             # Credentials (GITIGNORED)
        └── .env.example                     # Redacted template
```
