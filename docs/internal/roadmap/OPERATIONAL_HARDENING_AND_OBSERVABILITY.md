# Operational Hardening and Observability — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Monitoring, alerting, deployment hygiene, and secret rotation for the live product
> **Principle:** No infra complexity for its own sake. Every monitoring decision must answer: "What action would I take if this alert fired?"

---

## Operational Philosophy

This is a solo-founder product with a live hosted endpoint and a handful of paying clients. The operational posture is:

- **Minimal viable monitoring** — watch what matters, ignore what does not
- **Alert on actionable events only** — no dashboard nobody checks
- **Manual until automated is justified** — automate only when manual effort exceeds 30 minutes/week
- **Security hygiene is non-negotiable** — token rotation, secret management, and access control are always priority

---

## 1. Uptime Monitoring

### What to Monitor

| Endpoint | Purpose | Check Interval |
|----------|---------|---------------|
| `https://circle-mcp-server-production.up.railway.app/health` | Server is alive and responding | 5 minutes |
| `https://circle-mcp-server-production.up.railway.app/mcp` | MCP endpoint accepts connections | 15 minutes (synthetic check) |

### Recommended Tool

**UptimeRobot (free tier)** or **Better Stack (free tier)**

| Setting | Value |
|---------|-------|
| Check type | HTTP(S) |
| URL | `/health` endpoint |
| Interval | 5 minutes |
| Alert method | Email to founder |
| Response time threshold | 10 seconds (flag, not alert) |
| Downtime alert | After 2 consecutive failures |

### Action Plan on Downtime

| Duration | Action |
|----------|--------|
| < 5 min | Likely Railway deployment restart. Check Railway dashboard. Usually self-resolves. |
| 5–30 min | Check Railway logs for crash, OOM, or deployment failure. Manually restart if needed. |
| > 30 min | Check Circle API status. If Circle API is down, document and wait. If server issue, investigate logs and redeploy. |
| During a client session | Acknowledge immediately. Offer to reschedule. Do not debug live. |

### Setup Effort

30 minutes. Do on Day 1.

---

## 2. Secret Management

### Current State

| Secret | Storage | Rotation Schedule |
|--------|---------|-------------------|
| Circle API token | Railway environment variable + local `.env` | **IMMEDIATE rotation required** (prior exposure) |
| Railway deployment token | Railway dashboard | Rotate if compromised |

### Token Rotation Procedure

**Circle API Token:**

1. Log into Circle Admin → Settings → API Tokens
2. Generate new token with same permissions
3. Update Railway environment variable: `CIRCLE_API_TOKEN`
4. Update local `.env` file
5. Verify: hit `/health` endpoint and run a health check tool
6. Revoke old token in Circle Admin
7. Log rotation date in this document

**Rotation Schedule:**

| Secret | Frequency | Trigger |
|--------|-----------|---------|
| Circle API token | Every 90 days | Calendar reminder |
| Circle API token | Immediately | Any suspected exposure |
| Railway environment variables | As needed | Deployment changes |

### Secret Hygiene Rules

1. **Never commit secrets to git.** `.env` is in `.gitignore`. Verify before every push.
2. **Never include tokens in screenshots, demo recordings, or documentation.**
3. **Never share tokens in outreach messages, emails, or DMs.**
4. **Railway environment variables are the single source of truth for production secrets.**
5. **Local `.env` is for local development only.**

### Rotation Log

| Date | Secret | Action | Verified |
|------|--------|--------|----------|
| 2026-03-13 | Circle API token | **PENDING — must rotate before launch** | No |

---

## 3. Deployment Hygiene

### Current Deployment

| Attribute | Detail |
|-----------|--------|
| Platform | Railway |
| Runtime | Node.js |
| Entry point | `dist/http.js` |
| Build command | `npm run build` |
| Auto-deploy | On git push to main (via Railway) |
| Region | Default Railway region |

### Deployment Checklist (before every production push)

- [ ] All tests pass locally (`npm test`)
- [ ] Live API tests pass (`npm run test:live` — requires valid token)
- [ ] No secrets in committed code (grep for `CIRCLE_API_TOKEN`, `Bearer`, API keys)
- [ ] `dist/` is built and up to date (`npm run build`)
- [ ] Railway deployment succeeds (check Railway dashboard)
- [ ] Health endpoint responds after deploy (`/health`)
- [ ] One tool call works after deploy (e.g., `circle_get_community`)

### Rollback Procedure

1. Railway supports instant rollback to previous deployment
2. In Railway dashboard: Deployments → select previous successful deploy → Redeploy
3. Verify health endpoint after rollback
4. Investigate failure in the failed deploy before retrying

### Deployment Frequency

- **Stage 1:** Deploy only when a bug fix or hardening change is ready. No regular cadence.
- **Stage 2:** Deploy on feature releases (v0.4, v0.5). Test in local stdio before deploying HTTP.
- **Avoid:** Deploying during a client session or before a demo.

---

## 4. Logging and Diagnostics

### Current Logging

The server uses `console.log` and `console.error` for operational logging. Railway captures stdout/stderr.

### What to Log

| Event | Log Level | Purpose |
|-------|-----------|---------|
| Server startup | INFO | Confirm server is running, transport mode, port |
| Session created/destroyed | INFO | Track concurrent sessions |
| Tool call (name + duration) | INFO | Performance monitoring, usage patterns |
| Circle API error | ERROR | Diagnose API failures |
| Rate limit hit | WARN | Track API pressure |
| Session limit reached | WARN | Capacity planning |
| Token validation failure | ERROR | Security monitoring |

### What NOT to Log

| Item | Reason |
|------|--------|
| Circle API token value | Security — never log secrets |
| Full API response bodies | Privacy — may contain community member data |
| User IP addresses | Privacy — not needed for this product |
| Request headers | Unnecessary noise at this scale |

### Log Review Cadence

| Frequency | Action |
|-----------|--------|
| After every deployment | Check Railway logs for startup errors |
| Weekly (during launch) | Scan for recurring errors or unusual patterns |
| After client sessions | Check for errors that occurred during the session |
| Monthly (after launch) | General review, clean up if needed |

---

## 5. Performance Baselines

### Expected Performance

| Metric | Expected Range | Alert Threshold |
|--------|---------------|-----------------|
| Health check response | < 500ms | > 5 seconds |
| Tool call (read) | 500ms–2s | > 10 seconds |
| Tool call (write) | 1–3s | > 15 seconds |
| Memory usage | 50–150MB | > 500MB |
| Concurrent sessions | 1–5 typical | > 15 (approaching limit) |

### How to Measure

- **Response time:** UptimeRobot/Better Stack tracks health check latency
- **Tool call duration:** Add `console.time` / `console.timeEnd` to tool handlers (future improvement)
- **Memory:** Railway dashboard shows memory usage per deployment
- **Sessions:** Add session count to health check response (Priority 4 in hardening plan)

### Capacity Planning

| Scenario | Impact | Action |
|----------|--------|--------|
| 1–5 concurrent users | Normal operation | No action needed |
| 5–15 concurrent users | Approaching session limit | Monitor closely, consider increasing limit |
| 15–20 concurrent users | At session limit | New connections rejected with clear message |
| 20+ concurrent users | Session limit exceeded | Requires architectural change (not expected in Stage 1) |

---

## 6. Incident Response (Solo Founder Edition)

### Severity Levels

| Level | Definition | Response Time |
|-------|------------|---------------|
| **P1: Service Down** | Hosted endpoint is unreachable, clients cannot connect | Within 1 hour |
| **P2: Degraded** | Some tools failing, slow responses, intermittent errors | Within 4 hours |
| **P3: Minor** | Cosmetic issues, non-critical tool errors, doc inaccuracies | Within 24 hours |

### P1 Response Procedure

1. Check Railway dashboard for deployment status
2. Check Railway logs for crash or error
3. If deployment issue: rollback to previous version
4. If Circle API issue: check Circle status page, document, and communicate to affected clients
5. If unknown: restart the Railway service
6. After resolution: send brief update to any clients who were affected
7. Document the incident in a simple log (date, cause, resolution, duration)

### Communication Template (for client-facing incidents)

> "Hi [name] — the Circle MCP endpoint experienced a brief disruption from [time] to [time]. The issue was [brief cause] and has been resolved. Your connection should be working normally now. Let me know if you see any issues."

---

## 7. Operational Cadence

### Daily (during launch week)

- [ ] Check uptime monitor — any alerts?
- [ ] Check Railway logs — any new errors?
- [ ] Review outreach tracking — any responses to follow up?

### Weekly (ongoing)

- [ ] Review Railway resource usage (memory, CPU)
- [ ] Scan logs for recurring patterns
- [ ] Update outreach tracking
- [ ] Check GitHub for new issues or stars

### Monthly

- [ ] Rotate Circle API token (every 90 days)
- [ ] Review and clean Railway logs
- [ ] Update documentation if any operational changes occurred
- [ ] Review this operational plan — is anything missing?

---

## 8. What NOT to Build for Operations

| Temptation | Why Not |
|------------|---------|
| Custom logging service | Railway logs are sufficient for < 20 users |
| Grafana/Prometheus stack | Overkill for a solo-founder product with < 20 concurrent sessions |
| Automated alerting pipeline | UptimeRobot email alerts are sufficient |
| Status page | Not needed until 10+ active clients depend on the endpoint |
| CI/CD pipeline | Railway auto-deploy from git push is sufficient |
| Load testing infrastructure | Not needed until session limit is regularly approached |
| Log aggregation service | Railway log viewer is sufficient for Stage 1 |

**Rule of thumb:** If the operational tool takes longer to set up than the problem it solves takes to handle manually per month, defer it.
