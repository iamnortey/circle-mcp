# Remote MCP Operations Runbook — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Operational procedures for the hosted Circle MCP HTTP transport

---

## Operational Model

| Aspect | Value |
|--------|-------|
| **Platform** | Railway |
| **Service Type** | Web Service (long-running Node.js) |
| **Instances** | 1 (single instance, in-memory sessions) |
| **Entrypoint** | `node dist/http.js` (`npm run start:http`) |
| **Health Endpoint** | `GET /health` |
| **MCP Endpoint** | `/mcp` (POST, GET, DELETE) |
| **Session Model** | In-memory, reaped after 30 min idle |
| **Upstream Dependency** | Circle Admin API v2 (`https://app.circle.so`) |

---

## Restart / Redeploy Procedure

### Planned Redeploy (code update)

```bash
# Push new code
git push origin main
# Railway auto-deploys. Monitor via:
railway logs --tail
```

**Impact:** All in-memory sessions are lost. MCP clients will receive connection errors and must re-initialize. This is expected and documented in the MCP protocol.

### Manual Restart (no code change)

Via Railway Dashboard:
1. Navigate to Service → Settings
2. Click "Restart"

Via CLI:
```bash
railway redeploy
```

### Emergency Stop

```bash
# Remove the service (stops immediately)
railway down

# Or via dashboard: Service → Settings → Remove Service
```

---

## Incident Checklist

When an issue is reported or detected:

### 1. Triage

- [ ] Check `/health` endpoint — is the service responding?
- [ ] Check Railway dashboard — is the service running?
- [ ] Check Railway logs — any crash or error output?
- [ ] Check Circle API status — is the upstream API available?

### 2. Common Issues

| Symptom | Cause | Resolution |
|---------|-------|------------|
| `/health` not responding | Service crashed or deploying | Check Railway logs, wait for deploy, or restart |
| 502 from Railway | Service not listening on PORT | Verify `PORT` env is read correctly, check startup logs |
| Tool calls return auth errors | Invalid `CIRCLE_API_TOKEN` | Rotate token in Circle Admin, update Railway variable |
| Tool calls return 429 | Circle API rate limit | Wait for rate limit window to reset (5 min). Consider reducing call frequency. |
| Sessions accumulating | Clients not closing sessions | Check `MAX_SESSIONS` and `SESSION_TTL_MS`. Reaper runs every 60s. |
| Memory growing | Session leak or response accumulation | Restart service. If persistent, investigate with verbose logging. |
| Slow responses | Circle API latency | Check Circle API directly. Not within our control. |

### 3. Escalation

If the issue cannot be resolved with the above:
1. Check Railway status page for platform incidents
2. Check Circle API documentation for known issues
3. Review recent commits for regressions
4. Rollback to previous deployment if needed

---

## Rate Limit Considerations

### Circle API Rate Limits

| Limit | Value | Window |
|-------|-------|--------|
| API requests | 2,000 | 5 minutes |

The HTTP client (`src/lib/http.ts`) handles rate limiting:
- 429 responses trigger automatic retry with `Retry-After` header respect
- Exponential backoff: 1s → 2s → 4s
- Maximum retry delay capped at 60 seconds
- Only GET requests are retried (mutations are never auto-retried)

### MCP Session Limits

| Limit | Default | Configurable |
|-------|---------|-------------|
| Max concurrent sessions | 20 | `MAX_SESSIONS` env var |
| Session idle timeout | 30 minutes | `SESSION_TTL_MS` env var |
| Request body size | 1 MB | Hardcoded in `http.ts` |

If session limit is reached, new connections receive `503 Service Unavailable`.

---

## Secret Rotation Procedure

### Circle API Token Rotation

1. **Generate new token** in Circle Admin → Settings → API
2. **Update Railway variable:**
   ```bash
   railway variables set CIRCLE_API_TOKEN=<new-token>
   ```
3. **Railway auto-redeploys** when variables change
4. **Verify:** `curl -s https://<url>/health` returns 200
5. **Revoke old token** in Circle Admin

**Downtime:** Brief (10-30 seconds during redeploy). All active sessions are lost — clients reconnect automatically.

### Railway Token Rotation

If using Railway CLI tokens in CI/CD:
1. Generate new token in Railway Dashboard → Account → Tokens
2. Update CI/CD secrets
3. Revoke old token

---

## Known Failure Modes

### 1. Deploy Kills Sessions

**Expected behavior.** Every deploy restarts the process, clearing in-memory sessions. MCP clients handle this by re-initializing.

**Mitigation:** Deploy during low-usage windows if possible. Sessions are lightweight and re-initialization is fast.

### 2. Circle API Outage

**Impact:** All tool calls fail with upstream errors. The MCP server itself remains healthy — `/health` still returns 200.

**Detection:** Tool calls return errors with `502` or `503` status codes from Circle.

**Mitigation:** The HTTP client retries GET requests automatically. Mutations surface the error to the MCP client. No action needed from the operator — Circle API recovery is upstream.

### 3. Memory Exhaustion

**Unlikely** given the lightweight nature of MCP sessions, but possible with very high session counts or responses.

**Detection:** Railway memory metrics exceed 256MB, or service becomes unresponsive.

**Mitigation:** Restart service. Lower `MAX_SESSIONS`. Investigate if a specific tool returns unexpectedly large responses.

### 4. Railway Platform Outage

**Impact:** Service unreachable. No requests served.

**Detection:** Railway status page, external monitoring.

**Mitigation:** Wait for Railway recovery. Consider backup deployment on Render or Fly.io if SLA requires it.

---

## Manual Validation Workflow

Run periodically or after any change:

```bash
BASE_URL="https://<service>.up.railway.app"

# 1. Health
echo "▸ Health check"
curl -sf "$BASE_URL/health" | jq .

# 2. Initialize session
echo "▸ Initialize MCP session"
INIT_RESPONSE=$(curl -s -D /dev/stderr -X POST "$BASE_URL/mcp" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc":"2.0","id":1,"method":"initialize",
    "params":{"protocolVersion":"2025-03-26","capabilities":{},
    "clientInfo":{"name":"validation","version":"1.0.0"}}
  }' 2>&1)
echo "$INIT_RESPONSE" | tail -1 | jq .

# 3. Extract session ID from headers
SESSION_ID=$(echo "$INIT_RESPONSE" | grep -i 'mcp-session-id' | awk '{print $2}' | tr -d '\r')
echo "Session: $SESSION_ID"

# 4. List tools
echo "▸ List tools"
curl -s -X POST "$BASE_URL/mcp" \
  -H "Content-Type: application/json" \
  -H "mcp-session-id: $SESSION_ID" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}' \
  | jq '.result.tools | length'

# 5. Close session
echo "▸ Close session"
curl -s -X DELETE "$BASE_URL/mcp" -H "mcp-session-id: $SESSION_ID"
echo "Done."
```

---

## Release Checklist for Future Versions

Before deploying a new version:

- [ ] Version bumped in `package.json`
- [ ] Version bumped in `src/server.ts` (MCP server info)
- [ ] Version bumped in `src/http.ts` (health endpoint)
- [ ] `npm run validate` passes (all 95+ tests)
- [ ] `npm run test:http` passes (all 15+ HTTP tests)
- [ ] CHANGELOG or release notes updated
- [ ] No new secrets or tokens committed
- [ ] README updated if new tools or breaking changes
- [ ] Deploy to Railway (auto or manual)
- [ ] Post-deploy verification: health + session lifecycle
- [ ] Smithery listing updated if tools changed
- [ ] Notify downstream consumers if breaking changes
