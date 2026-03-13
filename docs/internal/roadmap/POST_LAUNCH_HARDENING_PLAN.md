# Post-Launch Hardening Plan — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Top 10 post-launch priorities with severity ranking, 2-week and 30-day action plans
> **Scope:** What to fix, tighten, and stabilize after the first paid engagements land

---

## Hardening Philosophy

The product is live and selling. Hardening is not about perfection — it is about ensuring the first 5–10 clients have a reliable, trustworthy experience that generates referrals and testimonials.

Every item on this list earned its place by answering: **"If this breaks or is missing during a paid engagement, what happens?"**

---

## Top 10 Post-Launch Priorities

### Priority 1: Circle API Token Rotation

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🔴 CRITICAL — BLOCKING |
| **Risk** | Prior token exposure in a screenshot. Any public use before rotation is a security incident. |
| **Owner** | Founder (manual — Circle Admin API dashboard) |
| **Timeline** | Day 1 — before any public launch activity |
| **Definition of done** | New token generated, old token revoked, `.env` updated, Railway environment variable updated, hosted endpoint verified with new token |
| **Effort** | 15 minutes |

**Why critical:** Every launch announcement includes a live endpoint. If the exposed token is still active, a bad actor could use it. No launch activity should happen until this is done.

---

### Priority 2: Error Response Audit for Client-Facing Scenarios

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟠 HIGH |
| **Risk** | During a paid demo or setup session, a confusing error message erodes trust. The operator sees "Request failed" instead of "Your Circle API token does not have access to this space." |
| **Owner** | Founder (code) |
| **Timeline** | Week 1 (Days 2–5) |
| **Definition of done** | Review all error paths in the 3 write tools and 5 most-used read tools. Ensure every user-facing error includes: what went wrong, why, and what to try next. |
| **Effort** | 2–3 hours |

**Specific targets:**
- Invalid token → clear message: "Circle API token is invalid or expired. Generate a new token at..."
- Rate limit hit → message: "Circle API rate limit reached. The server will retry automatically."
- 404 on space/post → message: "Space/post not found. Verify the ID exists in your Circle community."
- Network timeout → message: "Could not reach Circle API. Check your network connection."

---

### Priority 3: First-Client Dry Run

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟠 HIGH |
| **Risk** | First paid session fails or feels unprepared. Bad first impression = no testimonial, no referral. |
| **Owner** | Founder (preparation) |
| **Timeline** | Before first paid session (ideally Day 3–5) |
| **Definition of done** | Complete the full Setup + Training workflow against a test community. Time each step. Document any friction points. Prepare fallback if something breaks during a live session. |
| **Effort** | 2 hours |

**Dry run checklist:**
- [ ] Fresh token configuration from scratch
- [ ] Claude Desktop stdio connection → health check
- [ ] Remote HTTP connection → health check
- [ ] Run through all 5 Prompt Starter Pack workflows
- [ ] Create a post, update it, add a comment
- [ ] Demonstrate unanswered post detection
- [ ] Time the full flow — should fit in 60 minutes

---

### Priority 4: Session Management Hardening

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟠 HIGH |
| **Risk** | The HTTP endpoint uses in-memory session store with 20 max concurrent sessions and 30-min idle TTL. If multiple demo users connect simultaneously during launch week, sessions may be evicted or conflict. |
| **Owner** | Founder (code) |
| **Timeline** | Week 1 (Days 3–7) |
| **Definition of done** | Add session count logging, implement graceful session limit messaging ("Server at capacity, please try again shortly"), verify TTL cleanup works under load. |
| **Effort** | 3–4 hours |

**Specific actions:**
- Add startup log line: "Session limit: 20, TTL: 30 min"
- When session limit is reached, return a clear MCP error (not a generic 500)
- Add session count to health check response (optional but useful for monitoring)
- Test with 5+ concurrent connections to verify behavior

---

### Priority 5: README and Quickstart Accuracy Pass

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟡 MEDIUM |
| **Risk** | Stale docs cause self-serve users to fail silently. A wrong path, missing step, or outdated config example wastes their time and generates support requests. |
| **Owner** | Founder (docs) |
| **Timeline** | Week 1 (Days 5–7) |
| **Definition of done** | Walk through every step in README.md and CLIENT_QUICKSTART.md from scratch. Verify every command, path, config snippet, and URL. Fix anything that does not work on first try. |
| **Effort** | 1–2 hours |

---

### Priority 6: Outreach Tracking System

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟡 MEDIUM |
| **Risk** | Without tracking, you cannot measure conversion rates, identify which segments respond, or follow up systematically. The GTM plan targets 30–50 outreach messages — you need to know which ones work. |
| **Owner** | Founder (operational) |
| **Timeline** | Day 1 (setup), ongoing (usage) |
| **Definition of done** | Simple spreadsheet with columns: Name, Segment, Channel, Date Sent, Response (Y/N), Response Date, Next Step, Outcome. Pre-populated with first 10 targets. |
| **Effort** | 30 minutes setup |

---

### Priority 7: Hosted Endpoint Uptime Monitoring

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟡 MEDIUM |
| **Risk** | The Railway endpoint could go down and you would not know until a client tells you. During launch week, endpoint availability is a credibility signal. |
| **Owner** | Founder (infra) |
| **Timeline** | Week 1 (Days 3–5) |
| **Definition of done** | Free uptime monitor (UptimeRobot, Better Stack, or similar) pinging the health endpoint every 5 minutes. Email alert on downtime. |
| **Effort** | 30 minutes |

**Specific setup:**
- Monitor URL: `https://circle-mcp-server-production.up.railway.app/health`
- Check interval: 5 minutes
- Alert: Email to founder
- Response time threshold: 10 seconds (flag slow responses)

---

### Priority 8: Write Tool Confirmation Patterns

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟡 MEDIUM |
| **Risk** | During a demo, an operator accidentally creates a real post in their production community. Non-destructive does not mean non-embarrassing. |
| **Owner** | Founder (code + docs) |
| **Timeline** | Week 2 (Days 8–12) |
| **Definition of done** | Document the "draft-first" pattern in the Prompt Starter Pack. Consider adding a `status: draft` default for `circle_create_post`. Update training materials to emphasize draft workflow. |
| **Effort** | 2 hours |

---

### Priority 9: Smithery Listing Verification

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟡 MEDIUM |
| **Risk** | Smithery listing goes live but has a rendering issue, wrong config, or broken endpoint. First MCP users who discover the listing try it, fail, and leave. |
| **Owner** | Founder (manual) |
| **Timeline** | Day 1–2 (immediately after publishing) |
| **Definition of done** | Submit listing, verify it renders correctly, test install flow from Smithery UI, confirm connection to hosted endpoint works. |
| **Effort** | 30 minutes |

---

### Priority 10: Testimonial Collection Process

| Attribute | Detail |
|-----------|--------|
| **Severity** | 🟢 LOW (but compounds) |
| **Risk** | First 3 clients have great experiences but you forget to ask for a testimonial. Three months later, you need social proof and have none. |
| **Owner** | Founder (operational) |
| **Timeline** | After each paid engagement |
| **Definition of done** | Template message ready: "Thanks for the session. Would you be willing to share a 2–3 sentence testimonial about your experience? I will use it on the GitHub repo and LinkedIn." Send within 24 hours of successful session. |
| **Effort** | 15 minutes to template, 2 minutes per ask |

---

## 2-Week Action Plan

| Day | Action | Priority |
|-----|--------|----------|
| 1 | Rotate Circle API token | P1 |
| 1 | Set up outreach tracking spreadsheet | P6 |
| 1 | Submit Smithery listing | P9 |
| 1 | Post LinkedIn launch announcement | GTM |
| 2 | Verify Smithery listing renders and connects | P9 |
| 2 | Post X/Twitter + GitHub Discussions | GTM |
| 2–5 | Error response audit (top 8 tools) | P2 |
| 3–5 | First-client dry run | P3 |
| 3–5 | Set up uptime monitoring | P7 |
| 3–7 | Session management hardening | P4 |
| 5–7 | README and quickstart accuracy pass | P5 |
| 6–10 | Send first 15 outreach messages | GTM |
| 8–12 | Write tool confirmation patterns | P8 |
| 10–14 | Follow up on Week 1 outreach | GTM |

## 30-Day Action Plan

| Week | Focus | Key Deliverables |
|------|-------|-----------------|
| 1 | Security + launch infrastructure | Token rotated, endpoint monitored, Smithery live, first outreach sent |
| 2 | Client readiness + outreach | Dry run complete, error messages improved, session hardening done, 15+ outreach sent |
| 3 | First conversions + feedback | 2–3 paid sessions delivered, testimonials requested, friction points documented |
| 4 | Iterate + expand | Offer refined based on feedback, second outreach wave, month-end retrospective |

---

## Severity Legend

| Level | Meaning |
|-------|---------|
| 🔴 CRITICAL | Blocks launch or creates security risk. Must be done before any public activity. |
| 🟠 HIGH | Failure here damages a paid engagement or first impression. Do in Week 1. |
| 🟡 MEDIUM | Missing this creates friction or missed opportunity. Do in Week 1–2. |
| 🟢 LOW | Compounds over time. Start the habit early. |

---

## Dependencies

| Item | Depends On | Status |
|------|-----------|--------|
| All launch activity | Token rotation (P1) | **PENDING** |
| Smithery listing | Hosted endpoint healthy | Live |
| First paid session | Dry run (P3) complete | Pending |
| Session hardening | Understanding of actual concurrent load | Estimated (low) |
| Testimonials | Successful paid engagements | Pending |
