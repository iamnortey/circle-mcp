# Feature Prioritization — v0.4 and v0.5

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Candidate features with ruthless prioritization — what to build, what to defer, and why
> **Principle:** Revenue-informed roadmap. Features earn their place through client demand, not developer enthusiasm.

---

## Prioritization Framework

Every candidate feature is evaluated on three axes:

| Axis | Question | Weight |
|------|----------|--------|
| **Revenue signal** | Have paying clients or prospects requested this? | High |
| **Effort** | How many hours of engineering work? | Medium |
| **Leverage** | Does this unlock a new segment, reduce delivery cost, or improve retention? | Medium |

Features that score high on revenue signal get built first. Features that only score on "would be cool" get deferred.

### Decision Categories

| Category | Meaning |
|----------|---------|
| ✅ **BUILD** | Validated demand, reasonable effort, clear leverage |
| ⏳ **STAGE** | Likely needed but not yet validated by client demand — prepare but do not build |
| ❌ **DEFER** | No revenue signal, high effort, or premature for current maturity |
| 🚫 **DO NOT BUILD** | Actively harmful to focus, positioning, or product quality at this stage |

---

## v0.4 Candidates

### ✅ BUILD: Event Tools (Read)

| Attribute | Detail |
|-----------|--------|
| **What** | `circle_list_events`, `circle_get_event` — read event data from Circle communities |
| **Revenue signal** | Circle communities with events are a large segment. Consultants auditing communities need event visibility. |
| **Effort** | Low (4–6 hours) — follows the same pattern as existing space/post/comment tools |
| **Leverage** | Expands audit capability. Consultants can now include event activity in health checks. |
| **Dependency** | Circle Admin API v2 event endpoints must be available |
| **Gating** | Build only after 2+ clients request event visibility during Stage 1 |

### ✅ BUILD: Course/Lesson Tools (Read)

| Attribute | Detail |
|-----------|--------|
| **What** | `circle_list_courses`, `circle_get_course`, `circle_list_lessons` — read course and lesson data |
| **Revenue signal** | Educators are a defined ICP. Course management is their primary workflow. |
| **Effort** | Low-Medium (6–8 hours) — similar CRUD pattern, may need nested resource handling |
| **Leverage** | Unlocks the educator segment. Setup + Training packages for educators become more valuable. |
| **Dependency** | Circle Admin API v2 course/lesson endpoints |
| **Gating** | Build after 1+ educator client engagement or 3+ educator-segment inquiries |

### ✅ BUILD: npm Global Install

| Attribute | Detail |
|-----------|--------|
| **What** | `npx circle-mcp` — install and run the server without git clone + build |
| **Revenue signal** | Every self-serve user who fails at setup is a lost conversion. npm install eliminates 3 steps. |
| **Effort** | Medium (4–6 hours) — package.json bin field, build pipeline, npm publish automation |
| **Leverage** | Highest leverage for Stage 2 transition. Reduces time-to-first-tool-call from 10+ minutes to 2 minutes. |
| **Dependency** | npm account, CI/CD for publishing |
| **Gating** | Build when 3+ self-serve users report setup friction, OR as Stage 2 entry work |

### ⏳ STAGE: Interactive Setup Validation

| Attribute | Detail |
|-----------|--------|
| **What** | On first run, the server validates the Circle API token, tests connectivity, and runs a health check automatically |
| **Revenue signal** | Not yet requested, but every setup session starts with manual verification |
| **Effort** | Medium (4–6 hours) — startup hook, token validation, diagnostic output |
| **Leverage** | Reduces setup support time. Makes self-serve onboarding viable. |
| **Gating** | Stage when setup friction is the #1 support request from self-serve users |

### ⏳ STAGE: Improved Error Diagnostics

| Attribute | Detail |
|-----------|--------|
| **What** | Structured error codes, suggested fixes, and links to documentation in every error response |
| **Revenue signal** | Will emerge from Stage 1 support interactions |
| **Effort** | Medium (6–8 hours) — error taxonomy, message templates, doc links |
| **Leverage** | Reduces support burden, improves self-serve success rate |
| **Gating** | Stage after documenting the top 5 error scenarios from first 5 clients |

### ❌ DEFER: Event Tools (Write)

| Attribute | Detail |
|-----------|--------|
| **What** | `circle_create_event`, `circle_update_event` — create and modify events |
| **Why defer** | Write tools require careful mutation safety, confirmation patterns, and testing. Read-first for events. |
| **Revisit** | After event read tools are validated and 2+ clients request write capability |

---

## v0.5 Candidates

### ✅ BUILD: Webhook Subscriptions (if validated)

| Attribute | Detail |
|-----------|--------|
| **What** | Subscribe to Circle events (new post, new member, new comment) and receive real-time notifications |
| **Revenue signal** | Not yet validated — depends on Stage 1 feedback. Real-time monitoring is a stated future capability. |
| **Effort** | High (12–20 hours) — webhook endpoint, event parsing, subscription management, reliability |
| **Leverage** | Transforms the product from pull-based (query on demand) to push-based (real-time alerts). Unlocks monitoring use cases. |
| **Dependency** | Circle Admin API v2 webhook support, persistent endpoint for receiving webhooks |
| **Gating** | Build ONLY if 3+ clients express need for real-time monitoring. Do not build speculatively. |

### ⏳ STAGE: Multi-Community Support

| Attribute | Detail |
|-----------|--------|
| **What** | Connect multiple Circle communities to a single server instance. Switch between communities in conversation. |
| **Revenue signal** | Agencies managing multiple client communities are a primary ICP. Multi-community is their workflow. |
| **Effort** | High (15–25 hours) — token management per community, session routing, context switching |
| **Leverage** | Unlocks the agency segment fully. Premium + Remote and Agency Enablement packages become significantly more valuable. |
| **Gating** | Stage when 2+ agency clients are paying and have explicitly requested multi-community |

### ⏳ STAGE: Pagination Improvements

| Attribute | Detail |
|-----------|--------|
| **What** | Automatic pagination for large result sets (communities with 500+ posts, 1000+ members) |
| **Revenue signal** | Will emerge naturally as clients with larger communities adopt the tool |
| **Effort** | Medium (6–10 hours) — cursor-based pagination, result streaming, truncation thresholds |
| **Leverage** | Removes a ceiling on community size the tool can handle effectively |
| **Gating** | Stage when a client reports truncated or incomplete results |

### ❌ DEFER: Dashboard / Analytics UI

| Attribute | Detail |
|-----------|--------|
| **What** | Web dashboard showing community health trends, tool usage, and session history |
| **Why defer** | The MCP interface is the product. A dashboard is a separate product. Building it now splits focus and adds maintenance. |
| **Revisit** | Stage 3 — only when self-serve user base exceeds 30 active connections |

### ❌ DEFER: Custom Workflow Builder

| Attribute | Detail |
|-----------|--------|
| **What** | Visual or config-based workflow automation (e.g., "every Monday, run health check and post summary") |
| **Why defer** | This is a platform feature, not a tool feature. Premature at < 20 active users. |
| **Revisit** | Stage 3 or beyond |

---

## Do Not Build (Active Exclusions)

These items are explicitly excluded from the roadmap. They are not deferred — they are rejected for stated reasons.

| Item | Why Not |
|------|---------|
| **Delete/archive tools** | Non-destructive design is a core positioning strength. Adding destructive operations changes the trust model and requires confirmation UX that does not exist yet. |
| **Moderation tools** | Moderation requires judgment, context, and reversibility. AI-driven moderation is a liability at this maturity level. |
| **Direct database access** | Circle's API is the integration surface. Direct DB access is a security and stability risk. |
| **Multi-platform support** (Discord, Slack, etc.) | Circle-first. Depth beats breadth. Platform expansion is a Stage 3+ conversation. |
| **Mobile app or native client** | The product is an MCP server. Claude Desktop and Claude Code are the clients. Building a client is building a different product. |
| **AI content generation** (beyond Claude's native capability) | The server provides data and tools. Content generation is Claude's job, not the server's. Adding AI generation to the server creates confusion about where intelligence lives. |
| **User authentication / multi-tenant** | Single-tenant, single-token is the correct model for v0.3–v0.5. Multi-tenant adds auth complexity that is premature. |

---

## Feature Roadmap Summary

```
v0.3.0 (Current) — 16 tools, dual transport, guided setup
│
v0.4 (Stage 2 entry)
├── Event tools (read)         ← BUILD when 2+ clients request
├── Course/lesson tools (read) ← BUILD when 1+ educator engagement
├── npm global install         ← BUILD as Stage 2 entry work
├── Setup validation           ← STAGE (prepare, build on demand)
└── Error diagnostics          ← STAGE (prepare, build on demand)
│
v0.5 (Stage 2 expansion)
├── Webhooks                   ← BUILD only if 3+ clients need real-time
├── Multi-community            ← STAGE when 2+ agencies are paying
└── Pagination improvements    ← STAGE when large-community client hits limits
│
v0.6+ (Stage 3)
├── Dashboard                  ← DEFER until 30+ active connections
├── Workflow builder           ← DEFER until Stage 3
└── Managed hosting            ← DEFER until self-serve economics validated
```

---

## Decision Log

| Date | Decision | Rationale |
|------|----------|-----------|
| 2026-03-13 | Event and course tools are v0.4, not v0.3.1 | No client demand yet. Build after Stage 1 validation. |
| 2026-03-13 | Webhooks are v0.5, gated on 3+ client requests | High effort, uncertain demand. Do not build speculatively. |
| 2026-03-13 | Multi-community is staged, not committed | Agency demand must be validated with revenue before investing 15–25 hours. |
| 2026-03-13 | Delete/archive tools are excluded, not deferred | Core positioning decision. Non-destructive is a feature, not a limitation. |
| 2026-03-13 | npm install is the highest-leverage Stage 2 feature | Eliminates the biggest onboarding friction for technical users. |
