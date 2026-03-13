# Product Maturity Roadmap — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Define the staged maturity journey from guided operator to partial self-serve, with gating criteria for each transition
> **Principle:** Guided-first, self-serve later. Revenue validates before investment scales.

---

## Maturity Philosophy

The product evolves in response to real demand, not aspirational roadmaps. Each stage has:
- **Entry criteria** — what must be true before this stage begins
- **Exit criteria** — what must be proven before advancing
- **Commercial motion** — how the product generates revenue at this stage
- **Kill signals** — what would cause us to pause or pivot

No stage is guaranteed. The product advances only when the current stage has been validated by paying users and measurable outcomes.

---

## Stage 1: Guided Product (Current — v0.3.0)

> "We set it up, train your team, and hand over a working AI community operations layer."

### What This Stage Looks Like

- 16 tools (13 read + 3 write)
- Dual transport: stdio (local) + Streamable HTTP (remote)
- Distribution: GitHub clone, hosted HTTP endpoint, Smithery marketplace
- Onboarding: guided setup with documentation, quickstart, prompt starter pack
- Commercial motion: productized services (Setup + Training as flagship)

### Entry Criteria

- [x] Server live and validated (95 tests, 19 live API tests)
- [x] Hosted endpoint operational on Railway
- [x] Documentation complete (README, quickstart, tool reference, prompt pack)
- [x] Service packaging defined (5-tier offer ladder)
- [x] Launch collateral ready (announcements, outreach copy, GTM plan)

### Exit Criteria (to advance to Stage 2)

| Criterion | Target | Why It Matters |
|-----------|--------|----------------|
| Paid engagements completed | 5+ | Proves the service model works and generates repeatable revenue |
| Client feedback collected | 5+ structured feedback sessions | Identifies the top 3 friction points and feature requests |
| Revenue generated | $3,000+ cumulative | Validates pricing and willingness to pay |
| Self-serve users (GitHub/Smithery) | 3+ who successfully connect without help | Proves the docs are usable for technical users |
| Testimonials collected | 2+ written testimonials | Social proof for next-stage outreach |
| Top 3 feature requests identified | Documented with frequency counts | Revenue-informed feature prioritization |

### Commercial Motion

| Offer | Price | Conversion Target |
|-------|-------|-------------------|
| Setup + Training ⭐ | $750–$1,250 | 60%+ of demo calls |
| Premium + Remote | $1,250–$2,000 | Teams with 2+ users |
| Agency Enablement | $2,000–$3,500 | Consultants who want to resell |
| Retainer | $200–$500/month | Upsell after successful setup |

### Kill Signals

- < 2 paid engagements in 30 days despite 30+ outreach messages → reassess ICP targeting
- 0 self-serve GitHub users in 30 days → docs are insufficient or the tool surface is wrong
- Consistent feedback that 16 tools is insufficient for any workflow → feature gap is blocking adoption

### Expected Duration

**2–4 months.** Stage 1 ends when the exit criteria are met, not on a calendar date.

---

## Stage 2: Smoother Guided Onboarding (v0.4–v0.5)

> "Connect your Circle community to Claude in 5 minutes — with docs good enough that most technical users can self-serve."

### What Changes

| Improvement | Purpose |
|-------------|---------|
| npm global install (`npx circle-mcp`) | Eliminates git clone + build step |
| Smithery one-click stdio setup | Reduces setup to a single marketplace install |
| Improved error messages and diagnostics | Self-serve users can troubleshoot without support |
| Event and course tools (v0.4) | Expands tool surface based on Stage 1 feedback |
| Webhook subscriptions (v0.5) | Enables real-time community monitoring |
| Interactive setup validation | Server verifies token, connection, and first tool call during setup |

### Entry Criteria

| Criterion | Detail |
|-----------|--------|
| Stage 1 exit criteria met | 5+ paid engagements, $3K+ revenue, 5+ feedback sessions |
| Top 3 feature requests validated | At least 3 clients independently requested the same features |
| npm packaging tested | `npx circle-mcp` works in a clean environment |
| Setup friction points documented | Specific steps where users get stuck are identified and addressable |

### Exit Criteria (to advance to Stage 3)

| Criterion | Target |
|-----------|--------|
| npm installs | 50+ in first 60 days |
| Self-serve success rate | 60%+ of npm installers complete a health check without support |
| Time-to-first-tool-call | < 10 minutes for a technical user |
| Support request rate | < 20% of new users need help |
| Revenue | $10,000+ cumulative |
| Active monthly connections | 15+ unique |

### Commercial Motion

- **Services continue** — Setup + Training remains available but delivery cost drops as onboarding improves
- **Lighter packages emerge** — "Quick Start" ($200–$400) for users who just need token help and a 15-min orientation
- **Self-serve users grow** — GitHub + npm + Smithery drive organic discovery
- **Retainer becomes standard** — Monthly support for active users

### Kill Signals

- npm installs < 10 in 60 days → distribution channel is wrong or product-market fit is weaker than expected
- Self-serve success rate < 30% → onboarding improvements failed, revisit the approach
- No new feature requests → user base is too small to generate signal

### Expected Duration

**3–6 months** after Stage 1 exit. Driven by feedback volume and engineering capacity.

---

## Stage 3: Broader Self-Serve Distribution (v0.6+)

> "Circle operators discover, install, and use Circle MCP without ever talking to the founder."

### What Changes

| Improvement | Purpose |
|-------------|---------|
| Self-serve onboarding wizard | Interactive setup that guides token creation, connection, and first workflow |
| Managed hosted option | No deployment — connect directly to a hosted instance |
| Usage dashboard | Monitor tool calls, session health, and community metrics over time |
| Multi-community support | Agencies can connect multiple Circle communities to one server |
| Subscription or usage-based pricing | Product revenue supplements or replaces service revenue |

### Entry Criteria

| Criterion | Detail |
|-----------|--------|
| Stage 2 exit criteria met | 50+ npm installs, 60%+ self-serve success, $10K+ revenue |
| Self-serve economics validated | Cost to support a self-serve user is < $10/month |
| Multi-community demand confirmed | 3+ agencies have requested multi-community support |
| Hosting economics modeled | Per-user hosting cost is sustainable at proposed price points |

### Exit Criteria

Stage 3 does not have a defined exit — it is the ongoing product state. Success is measured by:

| Metric | Target |
|--------|--------|
| Monthly active connections | 50+ |
| Monthly recurring revenue | $2,000+ |
| Self-serve onboarding success | 80%+ |
| Net promoter score | 40+ |
| Churn rate | < 10% monthly |

### Commercial Motion

- **Product revenue** — Subscription ($20–$50/month per community) or usage-based pricing
- **Premium tier** — Managed hosting, priority support, custom workflows
- **Service revenue continues** — Agency enablement, enterprise setup, custom integrations
- **Partner channel** — Circle consultants resell as part of their service offering

### Kill Signals

- Self-serve acquisition cost > lifetime value → hosting model is not viable
- Monthly connections plateau at < 20 → market is smaller than expected, pivot to niche
- Circle platform changes break core functionality → reassess platform dependency

### Expected Duration

**Ongoing.** Stage 3 is the target steady state. Reaching it is not guaranteed — it requires Stage 1 and Stage 2 validation.

---

## Maturity Transition Map

```
Stage 1: Guided Product (v0.3.0)          NOW
├── 16 tools, dual transport
├── Productized services ($750–$3,500)
├── Founder-delivered setup + training
│
├── EXIT GATE: 5+ paid, $3K+ revenue, 5+ feedback sessions
│
Stage 2: Smoother Onboarding (v0.4–v0.5)  MONTHS 3–6
├── npm install, Smithery one-click
├── Event/course tools, webhooks
├── Lighter service packages + self-serve growth
│
├── EXIT GATE: 50+ installs, 60%+ self-serve, $10K+ revenue
│
Stage 3: Self-Serve Distribution (v0.6+)   MONTHS 8–12+
├── Onboarding wizard, managed hosting
├── Multi-community, dashboard
├── Subscription revenue
└── Product-led growth
```

---

## What Does NOT Change Across Stages

These principles are constant regardless of maturity stage:

1. **Non-destructive writes** — delete/archive operations only when explicit confirmation patterns are implemented and tested
2. **Security-first** — token handling, rate limiting, and input validation remain strict
3. **Honest documentation** — every feature claim matches actual implementation
4. **Open-source core** — the GitHub repo remains free and open
5. **Circle-first focus** — no platform expansion until Circle is deeply served

---

## Revenue-Maturity Alignment

| Stage | Primary Revenue | Secondary Revenue | Monthly Target |
|-------|----------------|-------------------|----------------|
| Stage 1 | Service engagements ($750–$3,500 each) | None | $1,000–$3,000 |
| Stage 2 | Service engagements + lighter packages | Self-serve tips/donations (optional) | $3,000–$5,000 |
| Stage 3 | Subscriptions + premium hosting | Service engagements for enterprise | $5,000–$10,000 |

These targets are aspirational. Actual numbers will be validated by market response at each stage.
