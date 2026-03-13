# Environment Variable Contract — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Applies to:** Both stdio and HTTP transport modes

---

## Variable Reference

### Required Variables

| Variable | Type | Secret? | Default | Description |
|----------|------|---------|---------|-------------|
| `CIRCLE_API_TOKEN` | string | **YES** | — (fail-fast if missing) | Circle.so Admin API v2 token. Obtain from Circle Admin → Settings → API. |

### Optional Variables

| Variable | Type | Secret? | Default | Description |
|----------|------|---------|---------|-------------|
| `CIRCLE_BASE_URL` | string | No | `https://app.circle.so` | Circle API base URL. Override only for custom/proxy deployments. Trailing slashes are stripped automatically. |
| `PORT` | integer | No | `3000` | HTTP server listen port. **Auto-injected by Railway.** Do not set manually on Railway. |
| `MAX_SESSIONS` | integer | No | `20` | Maximum concurrent MCP sessions. Protects against resource exhaustion. |
| `SESSION_TTL_MS` | integer | No | `1800000` (30 min) | Session idle timeout in milliseconds. Sessions inactive beyond this are reaped. |

### Platform-Injected Variables

| Variable | Platform | Description |
|----------|----------|-------------|
| `PORT` | Railway | Auto-injected by Railway. The HTTP server reads this at startup. |
| `RAILWAY_ENVIRONMENT` | Railway | Set by Railway to `production` or custom environment name. Not read by the application but useful for conditional logic if needed. |
| `RAILWAY_SERVICE_NAME` | Railway | Service name in Railway project. Informational only. |

---

## Secret Classification

| Classification | Variables | Handling |
|---------------|-----------|----------|
| **Secret** | `CIRCLE_API_TOKEN` | Must be set via Railway encrypted variables or local `.env` (gitignored). Never committed to source. Never logged. |
| **Non-Secret** | All others | Safe to commit in documentation, railway.json, or CI config. |

---

## Local vs Production Differences

| Variable | Local Development | Railway Production |
|----------|------------------|-------------------|
| `CIRCLE_API_TOKEN` | Set in `.env` file (gitignored) | Set in Railway encrypted variables |
| `PORT` | Defaults to `3000` | Auto-injected by Railway |
| `MAX_SESSIONS` | `5` (for testing) | `20` (default, configurable) |
| `SESSION_TTL_MS` | `10000` (10s, for testing) | `1800000` (30 min default) |
| `CIRCLE_BASE_URL` | Default or custom | Default (`https://app.circle.so`) |

---

## Validation Behavior

### Startup Validation

The `loadEnvConfig()` function in `src/config/env.ts` performs fail-fast validation:

1. **`CIRCLE_API_TOKEN`**: If missing or empty after `.trim()`, the server throws with an actionable error message and exits with code 1.
2. **`CIRCLE_BASE_URL`**: Falls back to `https://app.circle.so`. Trailing slashes are stripped.
3. **`PORT`**: Parsed as integer with fallback to `3000`. No validation on range (Node.js will fail to bind if invalid).
4. **`MAX_SESSIONS`** / **`SESSION_TTL_MS`**: Parsed as integers with defaults. Invalid values parse to `NaN` — consider adding validation if needed.

### Error Output on Missing Token

```
Error: CIRCLE_API_TOKEN is not set. Set it in your environment or .env file. See .env.example for the expected format.
```

---

## .env.example

The canonical `.env.example` in the repo:

```bash
# Circle MCP Server — Environment Configuration
# Copy this file to .env and fill in your values.
# The .env file is gitignored and must never be committed.

# Required: Your Circle Admin API token
# Obtain from: Circle Admin > Settings > API
CIRCLE_API_TOKEN=

# Optional: Circle API base URL (defaults to https://app.circle.so)
# Only change this if you have a custom Circle deployment.
# CIRCLE_BASE_URL=https://app.circle.so

# Optional: HTTP transport settings (only used by npm run start:http)
# PORT=3000
# MAX_SESSIONS=20
# SESSION_TTL_MS=1800000
```

---

## Railway Setup Commands

```bash
# Set required secret
railway variables set CIRCLE_API_TOKEN=<token>

# Verify (shows masked value)
railway variables

# Optional: override defaults if needed
# railway variables set MAX_SESSIONS=50
# railway variables set SESSION_TTL_MS=3600000
```
