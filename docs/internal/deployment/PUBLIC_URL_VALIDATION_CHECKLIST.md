# Public URL Validation Checklist — Circle MCP HTTP Endpoint

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Verify the deployed Circle MCP HTTPS endpoint is fully operational

---

## Endpoint Reference

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `https://<service>.up.railway.app/health` | GET | Health check / readiness probe |
| `https://<service>.up.railway.app/mcp` | POST | MCP initialize + tool calls |
| `https://<service>.up.railway.app/mcp` | GET | SSE notification stream |
| `https://<service>.up.railway.app/mcp` | DELETE | Close MCP session |

**Base URL shape:** `https://<railway-service-name>.up.railway.app`
**MCP endpoint path:** `/mcp`
**Health endpoint path:** `/health`

---

## Validation Checks

### 1. HTTPS and TLS

```bash
# Verify HTTPS is working and certificate is valid
curl -v https://<service>.up.railway.app/health 2>&1 | grep -E "(SSL|TLS|subject|issuer|expire)"

# Verify HTTP redirects to HTTPS (Railway handles this)
curl -v http://<service>.up.railway.app/health 2>&1 | head -20
```

- [ ] HTTPS responds with valid TLS certificate
- [ ] Certificate is issued by a trusted CA (Railway uses Let's Encrypt)
- [ ] HTTP requests redirect to HTTPS

### 2. Health Endpoint

```bash
curl -s https://<service>.up.railway.app/health | jq .
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

- [ ] Returns `200 OK`
- [ ] `status` is `"ok"`
- [ ] `version` matches expected release
- [ ] `transport` is `"streamable-http"`
- [ ] `maxSessions` and `sessionTtlMs` match configured values

### 3. Environment Validation

```bash
# If health returns 200, CIRCLE_API_TOKEN is set (env loaded at startup)
# A missing token would prevent the server from starting at all

# Verify by checking Railway logs for startup message:
# [circle-mcp-http] Listening on http://localhost:<PORT>
```

- [ ] Server started successfully (implies `CIRCLE_API_TOKEN` is set)
- [ ] No error messages about missing environment variables in logs

### 4. MCP Session Initialization

```bash
# Initialize a new MCP session
curl -s -X POST https://<service>.up.railway.app/mcp \
  -H "Content-Type: application/json" \
  -D - \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2025-03-26",
      "capabilities": {},
      "clientInfo": {
        "name": "validation-test",
        "version": "1.0.0"
      }
    }
  }' 2>&1
```

- [ ] Returns `200` with JSON-RPC response
- [ ] Response headers include `mcp-session-id`
- [ ] Response body contains `serverInfo` with `name: "circle-so-mcp"`
- [ ] Response body contains `capabilities` with `tools` listing

### 5. Tool Call Validation

```bash
# Use the session ID from step 4
SESSION_ID="<mcp-session-id-from-headers>"

# List tools
curl -s -X POST https://<service>.up.railway.app/mcp \
  -H "Content-Type: application/json" \
  -H "mcp-session-id: $SESSION_ID" \
  -d '{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "tools/list",
    "params": {}
  }' | jq '.result.tools | length'
```

- [ ] Returns 16 tools
- [ ] Tool names match expected set (circle_list_spaces, circle_get_post, etc.)

### 6. Session Cleanup

```bash
# Close the session
curl -s -X DELETE https://<service>.up.railway.app/mcp \
  -H "mcp-session-id: $SESSION_ID"
```

- [ ] Returns `200` or `204`
- [ ] Subsequent requests with the same session ID return `404`

### 7. CORS Validation

```bash
# Preflight check
curl -s -X OPTIONS https://<service>.up.railway.app/mcp \
  -H "Origin: https://example.com" \
  -H "Access-Control-Request-Method: POST" \
  -D - -o /dev/null
```

- [ ] Returns `204`
- [ ] `Access-Control-Allow-Origin: *`
- [ ] `Access-Control-Allow-Methods` includes `GET, POST, DELETE, OPTIONS`
- [ ] `Access-Control-Allow-Headers` includes `Content-Type, mcp-session-id`

### 8. Error Handling

```bash
# Missing Content-Type
curl -s -X POST https://<service>.up.railway.app/mcp \
  -d '{}' | jq .
# Expected: 415 Unsupported Media Type

# Invalid JSON
curl -s -X POST https://<service>.up.railway.app/mcp \
  -H "Content-Type: application/json" \
  -d '{bad json' | jq .
# Expected: 400 Malformed JSON body

# Non-existent session
curl -s -X GET https://<service>.up.railway.app/mcp \
  -H "mcp-session-id: does-not-exist" | jq .
# Expected: 404 Session not found

# Non-existent path
curl -s https://<service>.up.railway.app/nonexistent | jq .
# Expected: 404 Not found
```

- [ ] 415 for missing Content-Type on POST
- [ ] 400 for malformed JSON
- [ ] 404 for non-existent session
- [ ] 404 for non-existent path
- [ ] No sensitive information in error responses

---

## Failure Scenarios and Diagnosis

| Symptom | Likely Cause | Diagnosis |
|---------|-------------|-----------|
| Connection refused | Service not running | Check Railway dashboard, view logs |
| 502 Bad Gateway | Service crashed on startup | Check logs for `CIRCLE_API_TOKEN` error or build failure |
| TLS error | Certificate not provisioned yet | Wait 2-3 minutes after first deploy for Let's Encrypt |
| `/health` returns but `/mcp` POST fails | Transport issue | Check MCP SDK version compatibility |
| Session ID not in response headers | Initialize payload malformed | Verify JSON-RPC format matches MCP spec |
| Tool call returns auth error | Invalid `CIRCLE_API_TOKEN` | Verify token in Railway variables, test against Circle API directly |
| 503 Session limit reached | Too many concurrent sessions | Check `MAX_SESSIONS` setting, check for session leaks |

---

## Automated Validation Script

Save this as `scripts/validate-remote.sh` for repeatable verification:

```bash
#!/usr/bin/env bash
set -euo pipefail

BASE_URL="${1:?Usage: validate-remote.sh <base-url>}"
PASS=0
FAIL=0

check() {
  local label="$1" expected="$2" actual="$3"
  if [ "$expected" = "$actual" ]; then
    echo "  ✓ $label"
    ((PASS++))
  else
    echo "  ✗ $label (expected: $expected, got: $actual)"
    ((FAIL++))
  fi
}

echo "▸ Health check"
HEALTH=$(curl -s "$BASE_URL/health")
check "status is ok" "ok" "$(echo "$HEALTH" | jq -r .status)"
check "transport is streamable-http" "streamable-http" "$(echo "$HEALTH" | jq -r .transport)"

echo "▸ 404 on unknown path"
STATUS=$(curl -s -o /dev/null -w "%{http_code}" "$BASE_URL/nonexistent")
check "returns 404" "404" "$STATUS"

echo "▸ CORS preflight"
STATUS=$(curl -s -o /dev/null -w "%{http_code}" -X OPTIONS "$BASE_URL/mcp")
check "returns 204" "204" "$STATUS"

echo ""
echo "Results: $PASS passed, $FAIL failed"
[ "$FAIL" -eq 0 ] && echo "All checks passed!" || exit 1
```
