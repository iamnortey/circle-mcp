# Smithery Readiness — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Checklist and steps for publishing Circle MCP Server to the Smithery marketplace

---

## Overview

Smithery is the MCP server registry/marketplace. Circle MCP Server has a `smithery.yaml` configured for **HTTP** transport (Streamable HTTP). The live Railway endpoint is validated and operational.

The project supports **three** distribution modes:
1. **Smithery-hosted (HTTP):** Smithery runs the server on their infrastructure — `smithery.yaml` with `type: http`
2. **Self-hosted (Railway HTTP):** Live at `https://circle-mcp-server-production.up.railway.app/mcp`
3. **Local (stdio):** User installs and runs locally via clone + build

For the comprehensive listing metadata and publish guide, see `docs/distribution/SMITHERY_PUBLISHING_PACKAGE.md`.

---

## Prerequisites Before Listing

### Infrastructure

- [ ] Railway deployment is live and healthy (`GET /health` returns 200)
- [ ] HTTPS endpoint is stable: `https://<service>.up.railway.app/mcp`
- [ ] TLS certificate is valid (auto-provisioned by Railway)
- [ ] `CIRCLE_API_TOKEN` is configured in Railway (server starts successfully)

### Code

- [ ] `npm run validate` passes (typecheck + build + 95 smoke tests)
- [ ] `npm run test:http` passes (15 HTTP transport tests)
- [ ] Version in `package.json`, `server.ts`, `http.ts`, and `smithery.yaml` are consistent
- [ ] README documents both stdio and HTTP usage

### Registry Configuration

- [ ] `smithery.yaml` updated with HTTP transport configuration (see below)
- [ ] Package metadata is complete: name, version, description, author
- [ ] License is specified (MIT)

---

## smithery.yaml Configuration

### Current (HTTP — publish-ready)

```yaml
startCommand:
  type: http
  configSchema:
    type: object
    required:
      - circleApiToken
    properties:
      circleApiToken:
        type: string
        title: "Circle API Token"
        description: "Circle.so Admin API token (v2). Required. Obtain from Circle Admin > Settings > API."
      circleBaseUrl:
        type: string
        title: "Circle Base URL"
        default: "https://app.circle.so"
        description: "Circle API base URL. Override only for custom or proxy deployments."
  commandFunction: |
    (config) => ({
      command: 'node',
      args: ['dist/http.js'],
      env: {
        CIRCLE_API_TOKEN: config.circleApiToken,
        ...(config.circleBaseUrl ? { CIRCLE_BASE_URL: config.circleBaseUrl } : {})
      }
    })
```

**Status:** Updated 2026-03-13. Uses `type: http` with Streamable HTTP transport and `dist/http.js` entrypoint. Self-hosted endpoint: `https://circle-mcp-server-production.up.railway.app/mcp`

---

## URL Readiness Gates

Before publishing to Smithery, all of these must be true:

| Gate | Verification |
|------|-------------|
| Health endpoint returns 200 | `curl -s https://<url>/health \| jq .status` → `"ok"` |
| MCP initialize succeeds | POST to `/mcp` returns session ID in headers |
| Tools list returns 16 tools | `tools/list` call returns complete tool set |
| Session lifecycle works | Create → use → delete session completes cleanly |
| No cold start delay | Response time < 2 seconds for health check |
| Version is consistent | package.json, /health, smithery.yaml all report same version |

---

## Metadata Consistency Checks

Before each Smithery publish, verify:

```bash
# package.json version
cat package.json | jq .version
# Expected: "0.3.0"

# Health endpoint version
curl -s https://<url>/health | jq .version
# Expected: "0.3.0"

# smithery.yaml (manual check — version should match if present)
cat smithery.yaml

# server.ts version
grep 'version:' src/server.ts
# Expected: version: "0.3.0"
```

- [ ] All version strings match
- [ ] Package name is `circle-so-mcp`
- [ ] Description is accurate and up-to-date
- [ ] Author field is set
- [ ] License is MIT

---

## Remote Endpoint Validation Steps

Run in sequence:

1. **Health:** `curl -sf https://<url>/health` → 200
2. **Initialize:** POST `/mcp` with MCP initialize → 200, session ID in header
3. **List Tools:** POST `/mcp` with `tools/list` → 16 tools returned
4. **Call Tool:** POST `/mcp` with `tools/call` for `circle_get_community` → response with community data
5. **Close Session:** DELETE `/mcp` with session ID → 200/204
6. **Session Gone:** GET `/mcp` with closed session ID → 404

---

## Listing Acceptance Checklist

- [ ] smithery.yaml is valid and committed
- [ ] npm package is buildable from clean checkout (`npm install && npm run build`)
- [ ] stdio mode works locally (`echo '{}' | node dist/index.js` starts without crash)
- [ ] HTTP mode works remotely (all URL readiness gates pass)
- [ ] README includes installation instructions for both modes
- [ ] No secrets in committed code (grep for API tokens, passwords)
- [ ] Test suite passes: `npm run validate && npm run test:http`
- [ ] Tool descriptions are clear and accurate (in `src/tools/*.ts`)
- [ ] Error messages are user-friendly (no stack traces in MCP responses)

---

## Proof Artifacts to Capture

Before submitting the Smithery listing, capture and store:

| Artifact | Format | Location |
|----------|--------|----------|
| Health check response | JSON | `docs/evidence/smithery-health-check.json` |
| MCP initialize response | JSON | `docs/evidence/smithery-init-response.json` |
| Tools list response | JSON | `docs/evidence/smithery-tools-list.json` |
| Tool call response (circle_get_community) | JSON | `docs/evidence/smithery-tool-call.json` |
| Validation suite output | Text | `docs/evidence/smithery-validation-run.txt` |
| HTTP smoke test output | Text | `docs/evidence/smithery-http-smoke.txt` |

These artifacts serve as audit trail for the Smithery listing review.

---

## Post-Listing Monitoring

After Smithery listing is live:

1. Monitor Railway logs for unexpected error patterns
2. Check session counts via `/health` — should not accumulate beyond expected
3. Verify tool calls from Smithery-connected clients work end-to-end
4. Monitor Circle API rate limits (2,000 requests per 5 minutes)
