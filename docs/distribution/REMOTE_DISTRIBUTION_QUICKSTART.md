# Remote Distribution Quickstart — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Concise reference for connecting to the hosted Circle MCP Server endpoint

---

## Public Endpoint

```
https://circle-mcp-server-production.up.railway.app/mcp
```

Health check (no auth required):
```
https://circle-mcp-server-production.up.railway.app/health
```

---

## What Remote Clients Need

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "circle": {
      "url": "https://circle-mcp-server-production.up.railway.app/mcp"
    }
  }
}
```

Restart Claude Desktop. The 16 Circle tools appear automatically.

### Claude Code CLI

```bash
claude mcp add circle --transport http https://circle-mcp-server-production.up.railway.app/mcp
```

### Other MCP Clients

Any client supporting MCP Streamable HTTP transport can connect to the endpoint above. The server implements MCP protocol version 2025-03-26.

**Important:** The remote endpoint has the Circle API token pre-configured on the server side. Remote clients do not need to provide a token — they connect and use tools immediately.

---

## What Local Users Need

Most users should install locally instead of using the remote endpoint. See the [Claude Code Install Guide](../self-serve/CLAUDE_CODE_INSTALL.md) or [Claude Desktop Install Guide](../self-serve/CLAUDE_DESKTOP_INSTALL.md) for the primary setup paths.

Quick version:

1. **Node.js >= 18** and a **Circle Admin API token**
2. Clone: `git clone https://github.com/iamnortey/circle-mcp-server.git`
3. Build: `cd circle-mcp-server && npm install && npm run build`
4. Configure Claude Code or Claude Desktop with the stdio transport

Local users provide their own Circle API token via the `CIRCLE_API_TOKEN` environment variable.

---

## Current Maturity

| Aspect | Status |
|--------|--------|
| Technical validation | Complete — 9/9 tests pass against live endpoint |
| Tool coverage | 16 tools (13 read + 3 write), all non-destructive |
| Transport | Streamable HTTP, MCP 2025-03-26 |
| Hosting | Railway, auto-deploy from GitHub, HTTPS enforced |
| Target users | Technical operators, community consultants, educators |
| Self-serve onboarding | Not yet — setup requires MCP client configuration |
| Consumer-grade UX | Not yet — this is an operator tool, not a consumer product |

---

## Validate the Connection

### Quick health check (no MCP client needed)

```bash
curl -sf https://circle-mcp-server-production.up.railway.app/health | python3 -m json.tool
```

Expected response:
```json
{
    "status": "ok",
    "version": "0.3.0",
    "transport": "streamable-http",
    "sessions": 0,
    "maxSessions": 20,
    "sessionTtlMs": 1800000
}
```

### First MCP call (via Claude)

After configuring your MCP client, type:

> "Give me a health snapshot of my Circle community"

You should see your community name, total spaces, posts, and members. If you see data, the connection is working.

---

## First Prompts to Try

| Prompt | What It Does |
|--------|-------------|
| "Give me a community health snapshot" | Aggregates member, post, and space counts |
| "List all spaces in my community" | Shows every space with name, type, and URL |
| "Are there any unanswered posts?" | Finds posts with zero comments |
| "Search for posts about onboarding" | Cross-community content search |
| "Create a draft post in space [ID] titled 'Test'" | Creates a draft post (non-destructive) |

See [PROMPT_STARTER_PACK.md](../product/PROMPT_STARTER_PACK.md) for the full prompt library.

---

## Known Limitations (v0.3.0)

- **No destructive operations** — cannot delete, archive, or bulk-remove content
- **No moderation** — cannot hide, flag, or remove posts/comments
- **No member management** — cannot invite, ban, or change roles
- **No space/community settings** — cannot create spaces or modify configuration
- **No events or courses** — planned for v0.4
- **No historical analytics** — health snapshots are point-in-time, not trend data
- **No file/image uploads** — posts are text/HTML only
- **Session-based** — each client gets an independent session; sessions expire after 30 minutes idle
- **Single community** — each server instance connects to one Circle community

---

## Security Notes

- The remote endpoint uses HTTPS (TLS enforced, HTTP/2)
- CORS is enabled (`Access-Control-Allow-Origin: *`) for cross-origin MCP clients
- Request body size is limited to 1 MB
- Maximum 20 concurrent sessions with automatic idle reaping
- Write operations (create/update) use a zero-retry policy to prevent duplicate content
- The Circle API token is stored as a Railway environment variable — never in code or logs
- All tool inputs are validated with Zod `.strict()` schemas

---

## Support and Escalation

| Need | Action |
|------|--------|
| Bug report | [GitHub Issues](https://github.com/iamnortey/circle-mcp-server/issues) |
| Connection help | Check `/health` endpoint first, then verify MCP client config |
| Feature request | [GitHub Issues](https://github.com/iamnortey/circle-mcp-server/issues) |
| Circle API issues | Check [Circle platform status](https://status.circle.so) |
| Token issues | Regenerate in Circle Admin > Settings > API |

---

## Architecture Reference

```
MCP Client (Claude Desktop / Claude Code / any MCP client)
    │
    │ Streamable HTTP (HTTPS)
    ▼
Railway Endpoint (/mcp)
    │
    │ Session management, CORS, body parsing
    ▼
MCP Server Layer (16 tool handlers, Zod validation)
    │
    │ HTTPS (native fetch)
    ▼
Circle Admin API v2 (app.circle.so)
```
