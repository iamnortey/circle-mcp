# Smithery Distribution — Technical Assessment

> **Version:** 1.0.0
> **Date:** 2026-03-13
> **Author:** Director (Claude Code)
> **Status:** Deliverable — ready for decision

---

## 1. Executive Summary

**The server is already Smithery-compatible.** The engineering work is done. There is no architecture gap, no missing transport, and no code to write.

The circle-mcp-server already ships:

- A production-hardened Streamable HTTP entrypoint (`src/http.ts`, 424 lines)
- A valid `smithery.yaml` with `type: http`, config schema, and command function
- A live Railway deployment at `https://circle-mcp-server-production.up.railway.app/mcp`
- A `package.json` with `smithery.yaml` in the `files` array, correct `bin`, `repository`, `homepage`, and `keywords` fields
- Session management (20 max, 30-min TTL, automatic reaping)
- Health endpoint, CORS, body size limits, graceful shutdown
- CI passing on Node 18/20/22

The earlier publishing attempts failed due to **CLI usage issues, not architectural gaps:**

1. `smithery mcp publish [server]` with raw `dist/` artifacts failed because the CLI expected the repo root (which contains `smithery.yaml`), not a build output directory.
2. `--transport stdio` failed because the installed CLI version does not support that flag — it reads transport type from `smithery.yaml` instead.

**Recommendation: Publish now. The only remaining step is submitting the GitHub repo URL to Smithery.**

---

## 2. Compatibility Gap Analysis

| Requirement | Smithery Expectation | Current State | Gap |
|------------|---------------------|---------------|-----|
| **Transport** | Streamable HTTP (MCP 2025-03-26) | `src/http.ts` using `StreamableHTTPServerTransport` from SDK v1.27.1 | **None** |
| **Manifest** | `smithery.yaml` at repo root with `startCommand` block | Present, valid, `type: http` with config schema | **None** |
| **Config schema** | JSON Schema for user-provided config (API tokens, etc.) | `circleApiToken` (required), `circleBaseUrl` (optional) | **None** |
| **Command function** | JS function returning `{command, args, env}` | Returns `node dist/http.js` with env injection | **None** |
| **HTTP endpoints** | `POST /mcp`, `GET /mcp` (SSE), `DELETE /mcp` | All three implemented with session management | **None** |
| **Health check** | Recommended but not required | `GET /health` returning JSON with version and session count | **None** |
| **Package metadata** | `name`, `version`, `repository`, `description` in `package.json` | All present, `circle-so-mcp` v0.3.0 | **None** |
| **Build output** | `dist/` with compiled JS, shebang for CLI usage | `tsc` build, postbuild shebang injection | **None** |
| **npm publishability** | `files` array includes `dist`, `smithery.yaml`, `README.md`, `LICENSE` | All included | **None** |
| **Public GitHub repo** | Required for Smithery submission | `github.com/iamnortey/circle-mcp-server` | **None** |
| **Self-hosted URL** | Optional for URL-based publishing | Railway endpoint live and validated | **None** |
| **stdio fallback** | Optional for local installs | `src/index.ts` with `StdioServerTransport` | **None** |

**Gap count: 0.** Every Smithery requirement is already met.

---

## 3. Current-State Architecture

```
┌─────────────────────────────────────────────────────┐
│                  circle-mcp-server                    │
│                                                       │
│  src/server.ts    createServer() → McpServer (16 tools) │
│       │                                               │
│       ├── src/index.ts   StdioServerTransport         │
│       │   (local: Claude Desktop, Claude Code)        │
│       │                                               │
│       └── src/http.ts    StreamableHTTPServerTransport │
│           (hosted: Railway, Smithery)                 │
│                                                       │
│  smithery.yaml   type: http, configSchema, commandFn  │
│  railway.json    build + deploy config                │
│  package.json    npm-publishable, bin, files           │
└─────────────────────────────────────────────────────┘

Transport Matrix:
┌──────────┬────────────┬──────────────┬───────────────┐
│ Context  │ Transport  │ Entrypoint   │ Token Source   │
├──────────┼────────────┼──────────────┼───────────────┤
│ Local    │ stdio      │ dist/index.js│ User env var   │
│ Railway  │ HTTP       │ dist/http.js │ Railway env    │
│ Smithery │ HTTP       │ dist/http.js │ Smithery UI    │
└──────────┴────────────┴──────────────┴───────────────┘
```

The architecture already supports all three distribution modes with a clean separation:
- `createServer()` is transport-agnostic (called by both entrypoints)
- Each transport entrypoint handles its own lifecycle
- Token injection varies by context but the server code is identical

---

## 4. Target Architecture Recommendation

**No changes needed.** The current architecture is the target architecture.

The dual-entrypoint pattern (`index.ts` for stdio, `http.ts` for HTTP) is the standard approach used by production MCP servers. The `smithery.yaml` correctly points to the HTTP entrypoint. The config schema correctly maps user-provided tokens to environment variables.

There is no adapter, wrapper, or bridge to build.

---

## 5. Minimal Implementation Roadmap

Since the engineering is complete, the remaining work is **operational, not technical:**

### Step 1: Verify build and tests (5 minutes)

```bash
cd app/circle-mcp-server
npm run validate          # typecheck + build + 95 smoke tests
npm run test:http         # HTTP transport smoke tests
```

### Step 2: Verify Railway endpoint is live (2 minutes)

```bash
curl -sf https://circle-mcp-server-production.up.railway.app/health | python3 -m json.tool
```

Expected: `{"status":"ok","version":"0.3.0","transport":"streamable-http"}`

### Step 3: Publish to npm (5 minutes)

```bash
npm pack --dry-run        # verify package contents
npm publish --access public
```

Verify: `npx circle-so-mcp --help` or `npm info circle-so-mcp`

### Step 4: Submit to Smithery (10 minutes)

Two options:

**Option A: Dashboard submission (recommended)**
1. Go to https://smithery.ai
2. Submit new server
3. Provide GitHub repo URL: `https://github.com/iamnortey/circle-mcp-server`
4. Smithery reads `smithery.yaml` automatically
5. Add listing description (use the 50-word canonical description from SMITHERY_PUBLISHING_PACKAGE.md)
6. Submit

**Option B: CLI submission**
```bash
smithery mcp publish https://github.com/iamnortey/circle-mcp-server -n iamnortey/circle-so-mcp
```

Note: If the CLI rejects the command, fall back to Option A. The dashboard submission is more reliable and doesn't depend on CLI version compatibility.

### Step 5: Post-publish verification (10 minutes)

- Visit the Smithery listing URL
- Verify the configuration UI shows `circleApiToken` as required
- Connect a test client through Smithery
- Run `circle_get_community` to verify end-to-end connectivity
- Run `circle_list_spaces` to verify data access

### Step 6: Create GitHub release (5 minutes)

```bash
git tag v0.3.0
git push origin v0.3.0
```

Create release on GitHub with changelog highlighting:
- 16 tools (13 read + 3 write)
- Streamable HTTP transport
- Smithery marketplace listing
- npm package: `circle-so-mcp`

**Total estimated time: ~40 minutes of operational work. Zero code changes.**

---

## 6. Risks and Caveats

### 6.1 Token Security (Medium Risk)

| Deployment Mode | Token Handling | Risk |
|-----------------|---------------|------|
| **Local stdio** | User provides via env var, stays on their machine | **Low** — standard MCP pattern |
| **Railway self-hosted** | Single token in Railway env, shared by all connecting clients | **Medium** — all users see one community's data |
| **Smithery hosted** | Each user provides their own token via Smithery config UI | **Low** — per-user isolation via Smithery's config injection |

The Railway self-hosted mode has a structural limitation: it uses a single pre-configured token, so all connecting clients see the same community data and can perform write operations. This is acceptable for personal use or a known team, but not for public multi-tenant access.

Smithery's hosted mode solves this by injecting per-user config (including the token) into isolated server instances.

**Mitigation:** The REMOTE_DISTRIBUTION_QUICKSTART.md already documents this. No additional work needed.

### 6.2 Multi-User Session Isolation (Low Risk)

The HTTP entrypoint manages sessions in-memory with a 20-session cap and 30-minute TTL. Each session gets its own `McpServer` instance via `createServer()`.

However, all sessions within a single process share the same `CIRCLE_API_TOKEN`. Under Smithery's hosting model, this is not an issue because Smithery spawns a fresh process per user config. Under Railway, all sessions share one token by design.

**No action needed.** The architecture correctly handles both models.

### 6.3 Smithery CLI Version Mismatch (Low Risk)

Previous publish attempts failed due to CLI flag incompatibility. The dashboard submission path avoids this entirely.

**Mitigation:** Use dashboard submission (Option A) for the initial publish.

### 6.4 Comment Creation Limitation (Informational)

The `circle_create_comment` tool returns 401 with admin tokens. This was reconciled in the trust-and-consistency cleanup pass (2026-03-13) — all public docs now carry the caveat. No Smithery-specific action needed.

### 6.5 Should This Product Remain Local-First? (Strategic)

Yes. The local stdio installation should remain the primary recommended path because:

1. **Token stays on user's machine** — simplest security posture
2. **No session limits or TTL** — stdio is a persistent connection
3. **No hosting cost** — user runs on their own hardware
4. **Fastest latency** — no network hop to Railway/Smithery

Smithery and Railway are distribution channels, not replacements for local usage. The docs already reflect this priority (local install guides are the primary paths; remote is documented as an alternative).

---

## 7. Final Recommendation

**Proceed with Smithery publication now.**

Rationale:

1. **Zero engineering work remaining.** The transport, manifest, config schema, build pipeline, and deployment are all production-ready.
2. **The blocker was operational, not technical.** Previous CLI failures were usage errors, not architecture gaps.
3. **Risk is low.** Smithery's per-user config injection handles token isolation. The local-first recommendation remains unchanged.
4. **The documentation is ready.** SMITHERY_PUBLISHING_PACKAGE.md has the listing copy, canonical descriptions, and validation checklist.
5. **Time to publish: ~40 minutes** of sequential operational steps. No code changes, no architecture changes, no new dependencies.

The only decision to make is whether to do npm publish first (makes `npx circle-so-mcp` work) or Smithery submission first (gets marketplace visibility). Both are independent and can be done in either order. npm first is cleaner because it establishes the package identity before the marketplace listing references it.

---

## Appendix: Files Relevant to Smithery Publishing

| File | Purpose | Status |
|------|---------|--------|
| `smithery.yaml` | Smithery manifest (transport, config, command) | Ready |
| `package.json` | npm metadata, `files` array includes `smithery.yaml` | Ready |
| `src/http.ts` | Streamable HTTP entrypoint (424 lines, production-hardened) | Ready |
| `src/index.ts` | stdio entrypoint (25 lines, production) | Ready — no changes needed |
| `railway.json` | Railway deployment config | Ready — already live |
| `README.md` | Public documentation (server repo) | Ready |
| `docs/distribution/SMITHERY_PUBLISHING_PACKAGE.md` | Listing copy and checklist | Ready |
| `docs/distribution/REMOTE_DISTRIBUTION_QUICKSTART.md` | Remote connection guide | Ready |
