# Railway Deployment Plan — Circle MCP HTTP Transport

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Target:** Production deployment of Circle MCP Server over Streamable HTTP

---

## Deployment Architecture

```
Internet (HTTPS)
    │
    ▼
Railway Edge (TLS termination)
    │
    ▼
*.up.railway.app
    │
    ▼
┌─────────────────────────────────┐
│  circle-mcp-server (Node.js)    │
│  Entrypoint: dist/http.js       │
│                                 │
│  GET  /health   → health check  │
│  POST /mcp      → MCP calls     │
│  GET  /mcp      → SSE stream    │
│  DELETE /mcp    → close session  │
└─────────────────────────────────┘
    │
    ▼
Circle Admin API v2
(https://app.circle.so)
```

**Service type:** Web Service (long-running Node.js process)
**Instances:** 1 (single instance, in-memory session state)
**Region:** US-West (default) or closest to primary user base

---

## Prerequisites

1. Railway account (Hobby plan minimum: $5/month credit)
2. Railway CLI installed: `npm install -g @railway/cli`
3. `CIRCLE_API_TOKEN` from Circle Admin > Settings > API
4. Repository pushed to GitHub (Railway deploys from git)

---

## Step-by-Step Deployment

### 1. Create Railway Project

```bash
# Login to Railway
railway login

# Initialize project (from app/circle-mcp-server/)
cd app/circle-mcp-server
railway init
```

Or via Railway Dashboard: New Project → Deploy from GitHub Repo.

**Important:** Set the root directory to `app/circle-mcp-server` in Railway project settings if deploying from the monorepo root.

### 2. Configure Build Settings

Railway auto-detects Node.js. Override if needed:

| Setting | Value |
|---------|-------|
| **Root Directory** | `app/circle-mcp-server` |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm run start:http` |
| **Node Version** | `20` (LTS, specified in `engines` as `>=18.0.0`) |

These are also codified in `railway.json` at the project root (created as part of this work).

### 3. Set Environment Variables

Via Railway Dashboard or CLI:

```bash
# Required
railway variables set CIRCLE_API_TOKEN=<your-circle-admin-api-token>

# Optional overrides (defaults are production-appropriate)
# railway variables set MAX_SESSIONS=20
# railway variables set SESSION_TTL_MS=1800000
# railway variables set CIRCLE_BASE_URL=https://app.circle.so
```

**Note:** `PORT` is auto-injected by Railway. Do not set it manually.

### 4. Deploy

```bash
# Deploy from local
railway up

# Or push to GitHub — Railway auto-deploys on push if connected
git push origin main
```

### 5. Verify Deployment

```bash
# Get the service URL from Railway dashboard or CLI
railway status

# Test health endpoint
curl -s https://<your-service>.up.railway.app/health | jq .

# Expected response:
# {
#   "status": "ok",
#   "version": "0.3.0",
#   "transport": "streamable-http",
#   "sessions": 0,
#   "maxSessions": 20,
#   "sessionTtlMs": 1800000
# }
```

---

## Health Check Configuration

Railway supports custom health checks. Configure in Dashboard:

| Setting | Value |
|---------|-------|
| **Health Check Path** | `/health` |
| **Health Check Interval** | 30 seconds |
| **Health Check Timeout** | 5 seconds |

The `/health` endpoint returns:
- `200 OK` with JSON body when the server is operational
- Response includes session count and configuration for observability

---

## Node Version

The project requires Node.js 18+ (`engines` field in `package.json`). Railway defaults to the latest LTS.

To pin explicitly, the `railway.json` specifies the build environment. Alternatively, add to project settings or use a `.node-version` file:

```
20
```

---

## Required Files

| File | Purpose | Created/Updated |
|------|---------|-----------------|
| `railway.json` | Railway deployment configuration | Created |
| `src/http.ts` | HTTP transport entrypoint | Existing (production-ready) |
| `package.json` | `start:http` script | Existing |
| `.env.example` | Environment template | Existing |

---

## Logs and Observability

### Viewing Logs

```bash
# Stream logs from CLI
railway logs

# Or view in Railway Dashboard → Service → Logs
```

### Log Format

All logs are written to stderr with prefix `[circle-mcp-http]`:

```
[circle-mcp-http] Listening on http://localhost:<PORT>
[circle-mcp-http] POST /mcp → 200 new-session
[circle-mcp-http] Session created abc12345… (1/20)
[circle-mcp-http] Reaping idle session abc12345…
[circle-mcp-http] SIGTERM received — shutting down…
```

### Key Metrics to Monitor

- Session count (from `/health` endpoint)
- Error rate in logs (grep for `→ 4` or `→ 5`)
- Memory usage (Railway dashboard)
- Response latency (Railway metrics)

---

## Rollback Plan

### Automatic Rollback

Railway maintains deploy history. To rollback:

1. Go to Railway Dashboard → Service → Deployments
2. Click on the previous successful deployment
3. Click "Rollback"

### CLI Rollback

```bash
# List recent deployments
railway deployments

# Rollback to previous
railway rollback
```

### Manual Rollback

```bash
# Revert the git commit and push
git revert HEAD
git push origin main
```

---

## Post-Deploy Verification Checklist

- [ ] `GET /health` returns `200` with `status: "ok"`
- [ ] `POST /mcp` with MCP initialize request returns session ID
- [ ] `GET /mcp` with session ID establishes SSE stream
- [ ] `DELETE /mcp` with session ID closes session cleanly
- [ ] `OPTIONS /mcp` returns CORS headers
- [ ] No secrets visible in Railway logs
- [ ] Memory usage is stable (not climbing)
- [ ] Health check is green in Railway dashboard

---

## Cost Estimate

| Component | Estimated Monthly Cost |
|-----------|----------------------|
| Railway Hobby Plan | $5 credit included |
| Node.js Web Service (idle/low traffic) | ~$2–3/month |
| **Total (low traffic)** | **$0–3/month** (within Hobby credit) |
| **Total (moderate traffic)** | **$5–10/month** |

Railway bills based on resource usage (CPU hours + memory). A lightweight MCP server serving occasional tool calls will use minimal resources.
