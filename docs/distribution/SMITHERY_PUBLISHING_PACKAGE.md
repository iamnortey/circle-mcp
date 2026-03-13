# Smithery Publishing Package — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Source document for Smithery marketplace listing quality and publish readiness

---

## Canonical Short Description

> MCP server for Circle.so — community intelligence and content operations via natural language. 16 tools for spaces, posts, members, comments, search, analytics, and content creation.

---

## Canonical Long Description

Circle MCP Server connects your Circle.so community to Claude and any MCP-compatible AI client. It provides 16 tools (13 read, 3 write) that translate natural language into structured Circle Admin API v2 calls.

**What it does:**
- Query spaces, posts, members, comments, topics, and community metadata
- Detect unanswered posts and generate community health snapshots
- Create and update posts, create comments — all from a conversational interface
- Handle pagination, retries, rate limits, and error formatting automatically

**What it does not do (v0.3.0):**
- No destructive operations (delete, archive, moderation)
- No member management (invite, ban, roles)
- No space/community settings changes
- No events or courses (planned for v0.4)

Built with TypeScript, Zod validation, and zero external HTTP dependencies (Node.js native fetch).

---

## Target Audience

| Segment | Use Case |
|---------|----------|
| **Community consultants and agencies** | Audit client communities in minutes, surface content gaps, push announcements |
| **Community strategists and operators** | Daily health checks, unanswered post triage, content creation from Claude |
| **Educators and cohort builders** | Post lessons, find unanswered student questions, update course content |
| **AI-native founders and solo operators** | Run community operations with AI leverage instead of a dedicated community manager |

**Current maturity:** The server is suitable for technical users and guided operator workflows. It is not a mass-market self-serve product.

---

## Supported Usage Modes

### Mode 1: Smithery-Hosted (HTTP)

When published to Smithery, the server runs on Smithery's infrastructure. Users connect through the Smithery platform and provide their Circle API token via the configuration UI.

- **Transport:** Streamable HTTP (MCP 2025-03-26)
- **Entrypoint:** `dist/http.js`
- **Configuration:** `circleApiToken` (required), `circleBaseUrl` (optional)

### Mode 2: Self-Hosted Remote (Railway HTTP)

The project maintains a live deployment on Railway. Teams and remote clients connect directly to the public endpoint.

- **Endpoint:** `https://circle-mcp-server-production.up.railway.app/mcp`
- **Health:** `GET /health` returns `{"status":"ok","version":"0.3.0"}`
- **Transport:** Streamable HTTP (MCP 2025-03-26)
- **Session management:** In-memory, 20 max concurrent, 30-min idle TTL

Client configuration:
```json
{
  "mcpServers": {
    "circle": {
      "url": "https://circle-mcp-server-production.up.railway.app/mcp"
    }
  }
}
```

### Mode 3: Local (stdio)

Users clone the repo, build, and run locally via stdio transport. Circle API token is provided via environment variable.

- **Entrypoint:** `dist/index.js`
- **Transport:** stdio
- **Configuration:** `CIRCLE_API_TOKEN` environment variable

Client configuration:
```json
{
  "mcpServers": {
    "circle": {
      "command": "node",
      "args": ["/path/to/circle-mcp-server/dist/index.js"],
      "env": {
        "CIRCLE_API_TOKEN": "YOUR_CIRCLE_API_TOKEN"
      }
    }
  }
}
```

---

## Live Public Endpoint

| Property | Value |
|----------|-------|
| URL | `https://circle-mcp-server-production.up.railway.app` |
| MCP endpoint | `/mcp` |
| Health endpoint | `/health` |
| Protocol | MCP 2025-03-26, Streamable HTTP |
| Hosting | Railway (us-east4, Nixpacks, auto-deploy from GitHub) |
| TLS | HTTPS enforced, HTTP/2 |
| Status | Live and validated (2026-03-13) |

---

## Capability Summary

| Category | Count | Details |
|----------|-------|---------|
| Read tools | 13 | Spaces, posts, members, comments, topics, search, community metadata, space groups, unanswered detection, health snapshot |
| Write tools | 3 | Create post, update post, create comment (comment creation subject to Circle API limitation — see note below) |
| Total | 16 | All non-destructive |

---

## Tool Summary

### Read Tools (13)

| Tool | Purpose |
|------|---------|
| `circle_list_spaces` | List community spaces with pagination and sorting |
| `circle_get_space` | Get a single space by ID |
| `circle_list_posts` | List posts with filtering by space, status, and text search |
| `circle_get_post` | Get a single post with full body content |
| `circle_list_members` | List community members with status filtering |
| `circle_search` | Search across the community by type |
| `circle_list_comments` | List comments on a post or in a space |
| `circle_get_comment` | Get a single comment by ID |
| `circle_list_topics` | List topics/tags in the community |
| `circle_get_community` | Get community metadata and settings |
| `circle_list_space_groups` | List space groups with space IDs |
| `circle_detect_unanswered_posts` | Find posts with zero comments |
| `circle_community_health` | Point-in-time community health snapshot |

### Write Tools (3)

| Tool | Purpose |
|------|---------|
| `circle_create_post` | Create a new post in any space (draft or published) |
| `circle_update_post` | Update an existing post (title, body, status, settings) |
| `circle_create_comment` | Create a comment on a post (known Circle API limitation: admin tokens may return 401 — tool handles gracefully with workaround) |

---

## Environment Variable Summary

| Variable | Required | Description |
|----------|----------|-------------|
| `CIRCLE_API_TOKEN` | Yes | Circle.so Admin API token (v2). Obtain from Circle Admin > Settings > API. |
| `CIRCLE_BASE_URL` | No | Circle API base URL. Defaults to `https://app.circle.so`. Override for custom/proxy deployments. |
| `PORT` | No | HTTP server port. Defaults to `3000`. Set automatically by Railway. |
| `MAX_SESSIONS` | No | Maximum concurrent MCP sessions. Defaults to `20`. |
| `SESSION_TTL_MS` | No | Session idle timeout in milliseconds. Defaults to `1800000` (30 minutes). |

**Security note:** Never commit API tokens to source control. Use environment variables or secrets management.

---

## Validation Proof Summary

Full validation completed 2026-03-13 against the live Railway endpoint:

| Test | Result |
|------|--------|
| Health check (`GET /health`) | PASS — 200, version 0.3.0 |
| MCP initialize (JSON-RPC) | PASS — session ID returned |
| Tools/list (16 tools) | PASS — 13 read + 3 write |
| Session lifecycle (create/use/close) | PASS |
| Error handling (400/404/406/413/415) | PASS |
| CORS headers | PASS |

Evidence files:
- `docs/internal/evidence/RAILWAY_LIVE_VALIDATION.md`
- `docs/internal/evidence/LIVE_TOOL_SURFACE.md`

---

## Listing Readiness Checklist

### Smithery Configuration

- [x] `smithery.yaml` uses `type: http` with Streamable HTTP transport
- [x] `configSchema` defines `circleApiToken` (required) and `circleBaseUrl` (optional)
- [x] `commandFunction` starts `dist/http.js` with proper env vars
- [x] `smithery.yaml` is included in `package.json` `files` array

### Package Metadata

- [x] Package name: `circle-so-mcp`
- [x] Version: `0.3.0`
- [x] Description is accurate and specific
- [x] Author: Isaac Nortey
- [x] License: MIT
- [x] Keywords include: `mcp`, `circle`, `circle-so`, `community`, `smithery`
- [x] Repository URL is set
- [x] Homepage URL is set

### Code Quality

- [x] `npm run validate` passes (typecheck + build + smoke tests)
- [x] HTTP transport tests pass (`npm run test:http`)
- [x] Live API tests pass (`npm run test:live`)
- [x] No hardcoded secrets in committed code
- [x] `.env.example` documents required variables with placeholders only

### Documentation

- [x] README documents both local and remote usage
- [x] Client Quickstart covers both setup paths
- [x] Tool reference is complete (16 tools)
- [x] Limitations are honestly documented

### Live Endpoint

- [x] Railway deployment is healthy (`GET /health` → 200)
- [x] MCP initialize succeeds with session ID
- [x] All 16 tools return from `tools/list`
- [x] Session lifecycle works (create → use → close)
- [x] CORS enabled for cross-origin clients
- [x] HTTPS enforced, HTTP/2

---

## Post-Publish Verification Checklist

After the Smithery listing goes live, verify:

- [ ] Listing appears on Smithery with correct name, description, and metadata
- [ ] Configuration UI shows `circleApiToken` as required field
- [ ] Connecting via Smithery establishes an MCP session
- [ ] `tools/list` returns all 16 tools through Smithery
- [ ] A read tool call (`circle_get_community`) returns data
- [ ] A write tool call (`circle_create_post` with `status: draft`) succeeds
- [ ] Rate limits are respected under normal usage

---

## Proof Artifacts to Capture After Listing

| Artifact | Purpose |
|----------|---------|
| Smithery listing URL | Canonical link to the published listing |
| Smithery listing screenshot | Visual proof of listing appearance |
| First successful tool call via Smithery | End-to-end validation through the marketplace |
| Install count (after 7 days) | Early adoption signal |

---

## What This Document Does Not Cover

- Pricing strategy for hosted service (see `docs/product/SERVICE_OFFER.md`)
- Marketing and outreach copy (Prompt 4/5)
- Post-launch monitoring and hardening (Prompt 5/5)
- npm registry publishing (future distribution channel)
