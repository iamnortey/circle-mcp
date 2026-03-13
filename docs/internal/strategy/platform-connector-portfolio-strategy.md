# Platform Connector Portfolio Strategy

> **Version:** 1.0.0
> **Status:** Internal Strategy Document
> **Last Updated:** 2026-03-12
> **Audience:** Founders, Principal Engineers, Product Strategy

---

## 1. Executive Summary

We are not building "more MCP servers." We are building a **portfolio of domain-specific agent connectors** for operator-heavy platforms where teams manage communities, learning, members, content, and engagement — but do so through fragmented UIs, shallow automations, and limited AI-native workflows.

The Circle MCP server is the proof of concept. It demonstrates the core pattern: authenticated API access, strict validation, structured outputs, pagination handling, and tool-oriented access to high-value community data. The architecture is modular, tested, and designed for reuse.

The next step is to extend that pattern into a focused platform portfolio where:

- The API surface is strong enough to build reliably.
- The buyer has real operational pain.
- The use cases are agent-friendly.
- The revenue potential justifies the effort.

This document defines **which platforms** to build for, **why**, **in what order**, and **how** — grounded in the architecture we already have.

---

## 2. Strategic Thesis

The opportunity is strongest where four conditions are true:

1. **The platform is core to operations**, not a side tool.
2. **The platform contains rich operational context**: members, content, events, courses, engagement, permissions, or workflows.
3. **Existing automation options are shallow**, generic, or expensive.
4. **Buyers care more about outcomes** than raw integrations.

This is why the strategy targets platforms like Bettermode, LearnWorlds, Hivebrite, Mighty Networks, Teachable, Disco, and Skool — rather than general consumer tools. These platforms sit at the center of community and learning operations. Their users are overloaded with admin work, under-served by current automation, and increasingly AI-curious.

The market timing is right. MCP adoption is accelerating. AI agents are moving from novelty to operational tool. The platforms themselves have matured their APIs. And the gap between "data stored in platform" and "data accessible to AI" is exactly what we fill.

---

## 3. Why Circle.so Matters

The Circle MCP server is more than a first product. It is a **reference architecture**, a **credibility asset**, and the **reusable foundation** for every connector that follows.

### Proof of Concept

The server ships 16 tools (13 read, 3 write), handles pagination, retries, rate limits, and error normalization. It runs in production against the Circle Admin API v2. It has 95 offline tests and 19 live API tests. It is not a prototype — it is a working system.

### Reference Architecture

The codebase establishes patterns that transfer directly to new connectors:

| Layer | Pattern | Reusability |
|-------|---------|-------------|
| **Transport** | stdio / JSON-RPC | Identical across connectors |
| **Server** | Tool registration orchestrator | Identical across connectors |
| **Tools** | One file per tool, register function pattern | Structural template |
| **Client** | Typed API client with method-per-endpoint | Adapt per platform API |
| **Schemas** | Zod `.strict()` with shared pagination fields | Reuse pagination; adapt domain fields |
| **HTTP** | Native fetch, retry with backoff, Retry-After | Reuse with config changes |
| **Errors** | Status-to-message mapping, `CircleApiError` | Generalize to `ConnectorApiError` |
| **Responses** | Dual-format (text + structuredContent), truncation guard | Identical across connectors |
| **Annotations** | `readOnlyHint`, `destructiveHint`, `idempotentHint` | Identical across connectors |

Key architectural decisions that should carry forward:

- **Zero external HTTP dependencies.** Native `fetch` (Node 18+) keeps connectors lean.
- **Strict TypeScript throughout.** No `any` types. Zod validation on all inputs.
- **Fail-fast configuration.** Missing credentials produce actionable errors at startup, not silent failures at call time.
- **Non-destructive by design.** Read-first philosophy. Write tools create and update; no deletes in v1.
- **Safe mutation path.** Zero-retry on writes to prevent duplicate creation.
- **Transparent derived tools.** Composite tools like `community_health` include computation metadata showing exactly which API calls were made.
- **Truncation transparency.** Oversized responses truncated at clean line breaks with detailed notice — no silent data loss.

### Credibility Asset

A well-built, documented, tested open-source MCP server signals engineering quality. It is the best demo for the portfolio thesis. Every future connector benefits from the trust the Circle server has already established.

---

## 4. Platform Prioritization Framework

Not every platform with an API is worth building for. The prioritization framework evaluates six dimensions:

| Dimension | What It Measures | Weight |
|-----------|-----------------|--------|
| **API Maturity** | Is the API public, stable, documented, and rate-limit-transparent? | High |
| **Operator Pain** | How much manual work do admins do that an agent could handle? | High |
| **Monetization Quality** | Does the platform's customer base have budget and operational complexity? | High |
| **Agent Workflow Fit** | Are the core operations (lookup, search, summarize, triage) agent-friendly? | Medium |
| **Distribution Potential** | Is there a clear path to reaching buyers (community, marketplace, agency)? | Medium |
| **Implementation Risk** | How likely is the API to change, break, or be restricted? | Medium |

A platform must score well on API Maturity and Operator Pain to be considered. Monetization Quality determines tier placement. The remaining dimensions influence sequencing within tiers.

---

## 5. Priority Platforms

### Tier 1: Build Now

#### A. Bettermode

**Why it matters.** Bettermode is the cleanest immediate wedge. Its positioning is business-facing and community-centric. Its developer surface is strong: GraphQL API, partner/developer tooling, and documented rate-limit behavior. Of all platforms evaluated, it has the most favorable combination of API quality and buyer sophistication.

**Who it is for.**
- Customer community teams at B2B SaaS companies
- Support and community operations teams
- Customer education teams
- Agencies managing branded communities

**Pain points.**
- Community insights are buried in UI layers.
- Moderation, member lookup, and content analysis are manual.
- Support and CS teams need community context but lack AI-native tooling.
- Workflows span multiple tools and break easily.

**Our value.**
- Direct agent access to members, spaces, posts, engagement, and moderation context.
- AI-assisted community health analysis, content triage, support summarization, and admin lookups.
- Elimination of shallow generic automations.

**What to build.**

*Phase 1 (Read):*
- List spaces / collections / channels
- Get member profile; list members with filters
- List posts / threads / discussions; get post detail
- Search across content
- Analytics summary tools
- Moderation queue read tools

*Phase 2 (Write + Workflows):*
- Create or draft post
- Tag / label / categorize
- Member provisioning / invite tools
- Workflow prompts: "community health report," "top unanswered support posts," "churn-risk members"

**GTM path.** Open-source GitHub repo. Registry distribution. Outreach to agencies and customer community leaders. Demos framed as "AI for customer community ops," not "MCP infrastructure."

**Recommendation:** Build first. Strongest execution wedge, excellent API, high-quality buyers.

---

#### B. LearnWorlds

**Why it matters.** LearnWorlds has public pricing, a real enterprise ladder, and a serious LMS footprint. The pricing signal shows a customer base with budget and operational complexity, especially at Learning Center and Corporate tiers.

**Who it is for.**
- Course businesses and training companies
- Internal academies and corporate enablement teams
- Education operators

**Pain points.**
- Learning data is fragmented across users, courses, progress, and content.
- Course ops teams spend time manually checking learner progress.
- Teams need AI help with reporting, curriculum analysis, and learner support.
- Generic automations are event-driven, not reasoning-driven.

**Our value.**
- Expose learner, course, enrollment, and activity context directly to an AI agent.
- Operators can ask natural-language questions: *Which cohorts are dropping off? Which lessons have the highest failure pattern? Which learners need intervention?*
- Smarter reporting and curriculum operations without dashboard fatigue.

**What to build.**

*Phase 1 (Read):*
- List courses; get course detail
- List users / learners; get learner profile
- List enrollments / progress
- Search courses and content
- Analytics roll-up

*Phase 2 (Write + Workflows):*
- Create enrollments
- Learner segmentation workflows
- AI prompt templates for cohort risk and curriculum review
- Summary generation for instructors and admins

**GTM path.** Target course businesses and training operators. Message around "AI learning operations" and "agent-enabled LMS admin." Publish demos showing learner insight workflows.

**Recommendation:** Build second. Best LMS wedge, operationally rich, strong monetization quality.

---

### Tier 2: Build After Initial Proof

#### C. Teachable

**Why it matters.** Teachable has broad market reach and low entry pricing, which means volume. Its pricing and plan structure indicate a large installed base, though many users are less sophisticated buyers than Bettermode or LearnWorlds customers.

**Who it is for.**
- Individual creators and small course businesses
- Digital product sellers
- Solo operators

**Pain points.**
- Solo creators are overloaded with admin work.
- Course, student, and sales operations are repetitive.
- Support and reporting are manual.
- Many users are AI-curious but not deeply technical.

**Our value.**
- Simplify repetitive course operations.
- Natural-language access to students, products, and progress.
- AI leverage without building brittle automation stacks.

**What to build.**
- List products/courses; list students
- Get student progress
- Search users and content
- Payout / transaction read summaries (if API allows)
- Prompt bundles for student support and creator ops

**GTM path.** Creator-facing demos. Low-friction hosted offering. "Connect Teachable to Claude" messaging.

**Recommendation:** Build third. Good reach, but weaker early monetization quality. Better as a volume play after core credibility is established.

---

#### D. Disco

**Why it matters.** Disco is AI-native in positioning, which creates a narrative advantage: an AI-forward platform still benefits from agent infrastructure around learning ops. Its cohort-based model creates rich operational context.

**Who it is for.**
- Cohort-based educators
- Academies and modern learning communities
- Operator-led learning programs

**Pain points.**
- Cohort health tracking is manual.
- Facilitators need insight across conversations, attendance, and progress.
- AI-native positioning does not automatically equal agent interoperability.

**Our value.**
- Agent access to cohort and community signals.
- Facilitator copilots for engagement and learning health.
- Summarization across discussions, events, and progress.

**What to build.**
- List cohorts/programs; list learners
- Get progress signals
- Discussion and event summaries
- Cohort health prompts

**GTM path.** Sell to modern academies and cohort operators. Emphasize "AI-native learning deserves agent-native infrastructure."

**Recommendation:** Build fourth. Learning-community differentiation play.

---

### Tier 3: Flagship / Enterprise

#### E. Hivebrite

**Why it matters.** Hivebrite is a stronger enterprise play than a fast initial wedge. Public pricing signals and positioning indicate higher-value institutional buyers and bespoke commercial motions. The platform serves alumni networks, associations, and large professional communities — organizations with operational complexity and procurement budget.

**Who it is for.**
- Alumni associations and universities
- Nonprofits and professional associations
- Large professional communities

**Pain points.**
- Member directories, events, mentoring, and engagement live in silos.
- Administrators need reporting and coordination support.
- Donor/member workflows are operationally heavy.
- Institutions need security, support, and trust.

**Our value.**
- Secure agent access to member, event, and engagement context.
- Reduced admin burden across directory management, event coordination, and reporting.
- AI-assisted matching, reporting, and community operations.
- Enterprise-safe hosted or self-hosted connector options.

**What to build.**
- List members; get member details
- List groups / chapters
- List events and attendees
- Search directory and resources
- Prompt workflows for engagement reporting, mentoring matches, chapter summaries

**GTM path.** Not self-serve first. Founder-led outreach. Pilot motion through institutions and associations. Package as managed connector / enterprise deployment.

**Recommendation:** Build as enterprise flagship once credibility is established through Tier 1 and 2 connectors.

---

### Tier 4: Watchlist / Opportunistic

#### F. Mighty Networks

**Why it matters.** Mighty now has a real Admin API in beta and stronger pricing tiers, which makes it more credible than before. However, API stability is not yet proven, and the beta status introduces execution risk.

**Who it is for.** Creators, brands, paid communities, course/community hybrids.

**What to build.** Member lookup, space/course/event discovery, engagement summary tools, content search and reporting.

**Recommendation:** Strong candidate for later build once API stability is clearer. Monitor beta progression.

#### G. Skool

**Why it matters.** Skool has significant creator energy and pricing clarity, but API risk remains the primary concern. The opportunity is real; the platform risk is also real. Developer surface remains weak compared with the other platforms evaluated.

**Who it is for.** Creators, coaches, paid communities, course-community operators.

**What to build (if API path is viable).** Member lookup, course/community content retrieval, leaderboard/engagement summaries, moderation/admin prompts.

**Recommendation:** Treat as experimental opportunity, not a cornerstone build. Revisit when API access matures.

---

## 6. Cross-Platform Pain Patterns

Despite differences in domain focus, the pain patterns across these platforms are remarkably consistent:

**Operational pain.** Admins waste time navigating dashboards, exporting data, answering repetitive questions, and manually finding records.

**Insight pain.** Important context exists but not in an AI-ready form. Users can store data but cannot reason over it naturally.

**Workflow pain.** Zapier and similar tools handle triggers, but are weak for analysis-heavy or multi-step contextual workflows that require reasoning.

**Support pain.** Teams need quick answers about members, courses, posts, or engagement. Current systems force manual lookup every time.

**Reporting pain.** Leaders want summaries, anomalies, and suggested actions — not just dashboards with raw numbers.

These patterns are not platform-specific. They are category-level problems. A portfolio approach addresses them systematically rather than one platform at a time.

---

## 7. Product Value Proposition

**What we are not selling:** "We connect APIs to AI."

**What we are selling:** We turn operator-heavy platforms into agent-accessible systems.

That means:

- **Direct access** to high-value platform context — members, content, courses, engagement, events.
- **Structured, reliable tools** with strict validation, typed responses, and transparent error handling.
- **Domain-aware prompts** that encode operational knowledge, not just API calls.
- **Lower friction** than stitching together Zapier, spreadsheets, dashboards, and manual admin work.
- **Optional hosted convenience** for teams that want outcomes without infrastructure management.

The value is not the protocol. The value is operational leverage.

---

## 8. Target Buyers

### A. Technical Operators and Agencies

Early adopters. They install, test, and evangelize. They care about depth, flexibility, and speed.

**Best initial offer:** Open-source connector, documentation, hosted endpoint option, implementation support.

### B. Operational Teams

Community managers, learning ops managers, support/community leads, academy admins. They care about outcomes, not protocol details.

**Best offer:** Hosted connector, guided setup, prompt packs, "AI connector" language rather than "MCP server" language.

### C. Enterprise Institutions

Universities, associations, nonprofits, B2B SaaS companies. They care about security, governance, uptime, and support.

**Best offer:** Managed deployment, enterprise auth, self-host or private deployment options, premium support and onboarding.

---

## 9. Product Scope and Connector Pattern

Every connector should follow the architectural pattern established by the Circle MCP server: layered separation of concerns, strict schemas, typed clients, pagination handling, and dual-format responses.

### Standard Connector Layers

```
1. Transport        │ stdio (JSON-RPC) — identical across connectors
2. Server           │ Tool registration orchestrator — identical across connectors
3. Config / Auth    │ Fail-fast env validation — adapt per platform auth model
4. Typed API Client │ Method-per-endpoint with typed params and returns — adapt per platform
5. Input Schemas    │ Zod .strict() with shared pagination fields — adapt domain fields
6. Tools            │ One file per tool, register function pattern — adapt per platform
7. HTTP             │ Native fetch, retry/backoff for reads, zero-retry for writes — reuse
8. Error Handling   │ Status-to-message mapping, actionable errors — generalize
9. Response Builder │ Dual-format output, truncation guard — identical across connectors
```

### Standard v1 Tool Categories

- List resources (spaces, courses, members, events)
- Get resource detail
- Search / discovery
- Member / user tools
- Content tools
- Analytics summary tools

### Standard v2 Tool Categories (post-validation)

- Write actions (create, update — no delete in early versions)
- Workflow prompts and composite tools
- Alerting / monitoring patterns
- Cross-platform normalization layer
- Hosted admin interface

### Architectural Principles to Preserve

- **Read-first, safe-first.** v1 connectors are read-only or read-heavy. Write actions ship after the read surface is validated.
- **Strict validation.** All inputs validated with Zod schemas in strict mode. Unknown properties rejected.
- **Transparent computation.** Derived/composite tools include metadata showing which API calls were made.
- **Bounded responses.** Pagination capped (1-100 per page). Text responses truncated with notice. Structured content preserved in full.
- **Actionable errors.** Every error includes the endpoint, status, and a human-readable remediation hint.
- **Tool annotations.** Every tool declares `readOnlyHint`, `destructiveHint`, and `idempotentHint` so clients can reason about safety.

---

## 10. Recommended Build Sequence

| Order | Platform | Tier | Rationale |
|-------|----------|------|-----------|
| 1 | **Bettermode** | Build Now | Strongest community wedge. Excellent API. High-quality buyers. |
| 2 | **LearnWorlds** | Build Now | Best LMS wedge. Operationally rich. Strong monetization quality. |
| 3 | **Teachable** | Build After Proof | Broader creator reach. GTM experimentation. |
| 4 | **Disco** | Build After Proof | Modern learning-community differentiation. |
| 5 | **Hivebrite** | Enterprise Flagship | High-ACV institutional wedge. Requires established credibility. |
| — | **Mighty Networks** | Watchlist | Monitor API beta stability. |
| — | **Skool** | Watchlist | Monitor API access maturity. |

This sequence delivers:
- One strong community wedge (Bettermode)
- One strong LMS wedge (LearnWorlds)
- One broader creator wedge (Teachable)
- One modern learning-community wedge (Disco)
- One high-ACV institutional wedge (Hivebrite)

That is a coherent portfolio, not random connector accumulation.

---

## 11. Go-to-Market Strategy

### Layer 1: Open-Source Credibility

GitHub repositories with strong READMEs, architecture docs, install guides, and examples. MCP registry distribution. The Circle implementation already demonstrates this pattern — every subsequent connector should match or exceed its documentation quality.

This layer builds trust with technical operators and creates organic discovery.

### Layer 2: Outcome-Led Content

Do not market the protocol first. Market the outcome.

- "Use Claude to manage your Bettermode community"
- "AI assistant for LearnWorlds operations"
- "Find at-risk learners and summarize course issues instantly"
- "Turn your community platform into an AI-readable operations layer"

Demos, walkthroughs, and case studies should show the problem being solved, not the architecture behind it.

### Layer 3: Agency and Operator Partnerships

Agencies that manage communities or academies are multipliers. They bring multiple clients and repeated deployment opportunities. A single agency partnership can produce more deployments than months of self-serve distribution.

### Layer 4: Founder-Led Enterprise Pilots

For Hivebrite-class buyers, the motion is:
- Direct outreach to institutional decision-makers
- Tailored demos showing their platform, their data model, their pain
- Pilot proposals with managed deployment
- Transition to enterprise contract after validation

---

## 12. Monetization Path

**Phase 1: Open Source + Services**
Open-source connectors. Revenue from implementation support, custom workflow development, and consulting. This validates demand without requiring product infrastructure.

**Phase 2: Hosted Connector Endpoints**
Managed hosted endpoints for teams that want convenience without self-hosting. Subscription pricing per connector or per platform.

**Phase 3: Enterprise Managed Deployments**
Private/self-hosted deployments with enterprise auth, governance, audit logging, and premium support. Contract pricing.

**Phase 4 (Potential): Portfolio Subscription**
Cross-platform bundle for organizations operating across multiple community/learning platforms. This only makes sense once three or more connectors are production-ready.

The key is not to force self-serve SaaS too early. At the beginning, services and managed setup will monetize faster and build deeper customer relationships.

---

## 13. Strategic Guardrails

**Build where APIs are real.** Do not let market excitement outrun technical feasibility. If a platform's API is in beta, undocumented, or unstable, it belongs on the watchlist — not the build queue.

**Sell outcomes, not protocol jargon.** "MCP" matters for developers. "AI operator connector" matters for buyers. Every GTM asset should lead with the operational problem, not the protocol.

**Reuse architecture aggressively.** The Circle server established the connector pattern. Every subsequent connector should reuse the transport, response builder, error normalization, truncation guard, and annotation patterns. Platform-specific code should be limited to the typed client, schemas, and tool handlers.

**Avoid platform sprawl.** A tight, high-quality portfolio of five to seven connectors beats chasing every community tool with a public API. Depth and reliability matter more than breadth.

**Prioritize trust.** For hosted versions, auth, token storage, observability, and governance are core product features — not afterthoughts. Enterprise buyers will evaluate security posture before evaluating features.

**Read-first, safe-first.** Every connector should launch with a read-heavy tool surface. Write actions ship after the read surface is validated in production. No delete operations in early versions.

---

## 14. Risks and Open Questions

### Execution Risks

- **API instability.** Platform APIs can change without warning, especially those in beta or with weak versioning guarantees. Mitigation: prioritize platforms with documented, versioned APIs. Build contract tests.
- **Rate limit pressure.** Agent workflows may hit rate limits faster than human usage patterns. Mitigation: implement retry with backoff and Retry-After respect (already done in Circle pattern). Surface rate-limit context in error messages.
- **Platform policy changes.** A platform could restrict API access, change pricing, or deprecate endpoints. Mitigation: diversify across platforms. Avoid single-platform dependency.
- **Connector maintenance burden.** Each connector is a living dependency. API changes, schema updates, and bug fixes accumulate. Mitigation: maximize shared infrastructure. Minimize platform-specific code surface.

### Strategic Open Questions

- **GraphQL vs. REST connector patterns.** Bettermode uses GraphQL; others use REST. How much of the shared architecture needs to flex for GraphQL? This needs investigation during the Bettermode build.
- **Cross-platform normalization.** Is there value in a unified data model across connectors (e.g., a common "member" or "content" type)? Or does domain-specific fidelity matter more? Defer this decision until three or more connectors are in production.
- **Hosted infrastructure timing.** When does it make sense to invest in hosted endpoint infrastructure vs. continuing with self-hosted open-source distribution? Likely after the second connector ships and demand signals are clearer.
- **Pricing model validation.** Per-connector pricing, per-platform pricing, or usage-based? Needs market validation through early customer conversations.
- **Write tool liability.** Write operations (creating posts, enrolling users, updating records) carry higher risk than reads. What is the right pace for expanding write surfaces across the portfolio?

---

## 15. Conclusion

We are building **domain-specific agent infrastructure for community and learning operations** — starting with the platforms where APIs are mature, operator pain is high, and AI-native workflows can deliver immediate leverage.

The Circle MCP server proves the pattern works. The architecture is modular, tested, and designed for reuse. The market has consistent pain across platforms. The buyer segments are clear. The build sequence is prioritized.

The portfolio strategy is:

- **Bettermode** as the community wedge.
- **LearnWorlds** as the LMS wedge.
- **Teachable** as the creator wedge.
- **Disco** as the modern learning wedge.
- **Hivebrite** as the enterprise flagship.
- **Mighty Networks** and **Skool** on watch.

Five connectors. Three buyer segments. One reusable architecture. A coherent portfolio — not random connector accumulation.
