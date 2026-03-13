# Hosting Platform Decision — Circle MCP HTTP Transport

> **Decision Date:** 2026-03-13
> **Status:** APPROVED
> **Decision:** Railway
> **Author:** Platform Architecture Review

---

## Context

Circle MCP Server requires a public HTTPS endpoint for remote MCP distribution via Streamable HTTP transport. The server is a long-running Node.js process using `node:http` with session-based state, SSE streaming, and graceful shutdown.

## Evaluation Criteria

| # | Criterion | Weight | Rationale |
|---|-----------|--------|-----------|
| 1 | Long-running Node.js process | HIGH | Server maintains in-memory sessions, not request/response |
| 2 | HTTP/SSE streaming | HIGH | MCP uses Server-Sent Events for notification streams |
| 3 | Environment variable management | HIGH | CIRCLE_API_TOKEN must be securely configured |
| 4 | Public HTTPS endpoint stability | HIGH | MCP clients connect to a deterministic URL |
| 5 | Deployment simplicity | MEDIUM | Single-command deploy from git push |
| 6 | Operational observability | MEDIUM | Logs, health checks, restart visibility |
| 7 | Cost at low traffic | MEDIUM | Prototype/early-stage usage patterns |
| 8 | Smithery publish compatibility | MEDIUM | Public URL must be stable and HTTPS |

## Platform Comparison

| Criterion | Railway | Render | Fly.io | Vercel |
|-----------|---------|--------|--------|--------|
| **Long-running process** | Native. Deploys as persistent service. | Native. Web services run continuously. | Native. Runs as Fly Machine VM. | **INCOMPATIBLE.** Serverless functions only; 30s–300s timeout. No persistent process. |
| **SSE streaming** | Full support. No request timeout on web services. | Full support. No forced timeout on paid plans. | Full support. Direct VM network. | **Limited.** Edge functions have streaming but with timeout constraints. |
| **Env management** | First-class UI + CLI. Encrypted at rest. Reference variables across services. | UI + CLI. Encrypted. Slightly less flexible. | CLI-primary (`fly secrets set`). UI available. | UI + CLI. Strong, but irrelevant given serverless limitation. |
| **HTTPS endpoint** | Auto-provisioned `*.up.railway.app`. Custom domains supported. | Auto-provisioned `*.onrender.com`. Custom domains. | Auto-provisioned `*.fly.dev`. Custom domains. | Auto-provisioned `*.vercel.app`. N/A for this use case. |
| **Deploy simplicity** | Git push or `railway up`. Zero config needed for Node. | Git push. Requires specifying build/start commands. | Requires `fly.toml` + `fly deploy`. More config overhead. | `vercel deploy`. Incompatible architecture. |
| **Observability** | Built-in log streaming, metrics, health checks. | Log streaming. Health check configuration. | Log streaming via `fly logs`. Prometheus metrics available. | Limited to function invocation logs. |
| **Cost (low traffic)** | Hobby: $5/month credit. Service runs ~$2–5/mo at low utilization. | Free tier: 750h/month (spins down after 15m inactivity). Paid: $7/mo minimum. | Free tier: 3 shared VMs. Pay-per-use after. | Free tier for serverless. N/A. |
| **Cold start risk** | None. Service runs continuously. | **YES on free tier.** Spin-down after 15m idle. 30–60s cold start. Paid tier avoids this. | Machines can auto-stop. Configurable. | Every request is cold. |
| **Smithery compatibility** | Stable URL, always-on. Ideal. | Stable URL but cold-start risk breaks MCP session initialization on free tier. | Stable URL, always-on if configured. | Incompatible. |
| **Session state** | In-memory sessions work — single persistent instance. | Works on paid tier. Free tier restarts kill sessions. | Works. Single machine is straightforward. | **Incompatible.** No in-memory state across invocations. |

## Decision

### **Recommended Platform: Railway**

### Rationale

1. **Architecture fit:** Railway deploys long-running Node.js services natively. The Circle MCP HTTP transport is a persistent `node:http` server with in-memory session state — this maps directly to Railway's service model with zero adaptation.

2. **Zero cold start:** Railway services run continuously. MCP session initialization requires an always-available endpoint. Render's free tier spin-down and Vercel's serverless model both introduce unacceptable latency or incompatibility.

3. **Minimal configuration overhead:** Railway detects Node.js projects automatically. `npm run build` + `npm run start:http` is all that's needed. No Dockerfile, no `fly.toml`, no platform-specific config file required (though `railway.json` is available for explicit control).

4. **Environment variable ergonomics:** Railway provides encrypted env vars via UI and CLI, with the ability to reference variables across services. `CIRCLE_API_TOKEN` and `PORT` (auto-injected by Railway) are all that's needed.

5. **Stable HTTPS URL:** Railway provisions `*.up.railway.app` with automatic TLS. This URL is stable across deploys and suitable for Smithery registration.

6. **Operational simplicity:** Log streaming, health check monitoring, one-click rollback, and deploy previews are built in. The operational surface matches the project's maturity stage.

### Rejected Platforms

| Platform | Rejection Reason |
|----------|-----------------|
| **Vercel** | **Architecturally incompatible.** Serverless function model cannot host a long-running Node.js process with in-memory session state and SSE streaming. Would require a complete rewrite to stateless request/response with external session storage. |
| **Render** | **Cold start risk on free tier** breaks MCP session reliability. Paid tier is viable but offers no meaningful advantage over Railway while requiring more explicit configuration for health checks and build commands. |
| **Fly.io** | **Viable but higher operational overhead.** Requires `fly.toml` configuration, Fly CLI for deployment, and more manual infrastructure management. Better suited for multi-region or container-heavy workloads, which are not current requirements. |

## Deployment Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Railway auto-injects `PORT` — code must honor it | LOW (already implemented) | HIGH if missed | `src/http.ts` reads `process.env.PORT` with fallback to 3000. Verified. |
| In-memory sessions lost on redeploy | MEDIUM | MEDIUM | Expected behavior. MCP clients re-initialize sessions. Session TTL is 30 minutes. Document in runbook. |
| Railway Hobby plan resource limits | LOW | LOW | Circle MCP is lightweight (~50MB memory). Well within Hobby limits. |
| CIRCLE_API_TOKEN exposure in logs | LOW (mitigated) | HIGH if exposed | Code never logs tokens. Verified in `src/http.ts` and `src/lib/http.ts`. |
| Railway service URL changes | VERY LOW | HIGH | URL is stable per service. Custom domain available as backup. |

## Future Considerations

- **Multi-region:** If latency becomes a concern, Fly.io's edge deployment model becomes attractive. Railway supports this via Railway Regions (beta).
- **Horizontal scaling:** Current in-memory session model limits to single instance. External session store (Redis) would be needed for multi-instance. Not required at current scale.
- **Custom domain:** Railway supports custom domains. Should be configured when the product has its own domain.
