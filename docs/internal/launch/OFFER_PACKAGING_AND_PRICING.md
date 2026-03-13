# Offer Packaging and Pricing — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Define the offer ladder, pricing guidance, and commercial packaging for early launch

---

## Offer Philosophy

The product is open source. The value-add is **expertise, speed, and guided outcomes.**

Buyers are not paying for software access — they are paying for:
- Setup done right the first time
- Training that maps to their specific workflows
- Remote deployment they do not have to manage
- Ongoing support as the product evolves
- A competitive advantage in their consulting/agency practice

---

## Offer Ladder

### Offer 1: Setup Only

> "We install and configure Circle MCP for your environment. You get a working connection in under an hour."

| Element | Detail |
|---------|--------|
| **Target buyer** | Technical operators who want setup speed, not training |
| **Deliverables** | Server install, API token configuration, Claude Desktop config, live verification (health check + tool test) |
| **Delivery** | 45–60 minutes, remote (video call or async) |
| **Pricing** | $400 – $750 |
| **What is not included** | Training, prompt pack walkthrough, remote deployment, ongoing support |

**When to offer:** Buyer is technical, already understands MCP or Claude, just wants it working fast.

---

### Offer 2: Setup + Training (Recommended Flagship)

> "We set up Circle MCP, train your team on workflows that matter for your community, and hand over everything you need to operate independently."

| Element | Detail |
|---------|--------|
| **Target buyer** | Community operators, strategists, educators who want to use the tools effectively |
| **Deliverables** | Full setup + 60-minute live training session + prompt starter pack delivery + personalized quickstart doc + 14 days email support |
| **Delivery** | ~2 hours total, remote |
| **Pricing** | $750 – $1,250 |
| **What is not included** | Remote HTTP deployment, multi-person team onboarding, ongoing retainer |

**When to offer:** Most engagements should start here. This is the flagship because it maximizes value-to-effort for both sides.

**Why this is the flagship:** Training is where the real value unlocks. A working server without prompt fluency is a tool that collects dust. Training converts setup into daily usage.

---

### Offer 3: Premium Setup + Remote Deployment

> "We set up Circle MCP locally and deploy a hosted endpoint for your team. Multiple team members can use it from Claude Desktop without running their own server."

| Element | Detail |
|---------|--------|
| **Target buyer** | Teams and agencies with 2+ users who need remote access |
| **Deliverables** | Full setup + training + Railway HTTP deployment + team onboarding (up to 5 seats) + 30 days email support |
| **Delivery** | ~3 hours total, remote |
| **Pricing** | $1,250 – $2,000 |
| **What is not included** | Ongoing hosting costs (Railway — typically $5–20/month), custom tool development, retainer |

**When to offer:** Buyer has a team. Multiple people need access. Local-only setup creates friction.

---

### Offer 4: Consultant / Agency Enablement Package

> "We enable your consulting practice or agency to offer Circle MCP as part of your client services. You get setup, training, deployment, and a white-label delivery playbook."

| Element | Detail |
|---------|--------|
| **Target buyer** | Circle consultants and agencies who want to resell or integrate this into their service offering |
| **Deliverables** | Full premium setup + agency-specific training (how to pitch, demo, and deliver to clients) + client delivery playbook + priority support (60 days) + early access to new features |
| **Delivery** | ~4 hours total across 2 sessions, remote |
| **Pricing** | $2,000 – $3,500 |
| **What is not included** | Per-client deployment (agency deploys for their own clients), custom feature development, revenue sharing |

**When to offer:** The buyer is a consultant or agency who sees Circle MCP as a competitive differentiator for their practice.

---

### Offer 5: Community Intelligence Retainer

> "Ongoing support, prompt updates, version upgrades, and priority access — so your Circle MCP setup stays current and effective."

| Element | Detail |
|---------|--------|
| **Target buyer** | Any setup client who wants ongoing support |
| **Deliverables** | Monthly check-in (30 min), version upgrade support, new prompt templates as tools expand, priority email support, early access to new features |
| **Delivery** | Ongoing, monthly |
| **Pricing** | $200 – $500/month |
| **Minimum commitment** | 3 months recommended, no hard lock-in |

**When to offer:** After a successful setup engagement. Upsell during the handoff phase.

---

## Pricing Summary

| Offer | Price Range | Typical Buyer |
|-------|-------------|---------------|
| Setup Only | $400 – $750 | Technical operators |
| Setup + Training ⭐ | $750 – $1,250 | Operators, strategists, educators |
| Premium + Remote | $1,250 – $2,000 | Teams, multi-user setups |
| Agency Enablement | $2,000 – $3,500 | Consultants, agencies |
| Retainer | $200 – $500/mo | Any existing client |

⭐ = Recommended flagship offer

---

## Pricing Rationale

| Factor | Justification |
|--------|---------------|
| **Value baseline** | Active Circle operators bill $75–$200/hour. The tool saves 5–10 hours/week of manual admin work. ROI is clear within the first month. |
| **Competitive landscape** | No direct competitor exists. Pricing reflects first-mover premium and genuine time savings. |
| **Delivery cost** | Guided setup takes 1–3 hours. Pricing covers preparation, delivery, documentation, and follow-up. |
| **Maturity adjustment** | Prices are at the lower end of what the value justifies because the product is v0.3.0. As the tool surface expands and onboarding simplifies, prices can increase. |
| **No race to the bottom** | The open-source repo is free. Services are premium because they deliver outcomes, not just access. |

---

## Boundaries and Exclusions

All offers explicitly **do not include**:

| Exclusion | Reason |
|-----------|--------|
| Custom tool development | Feature work is on the product roadmap, not per-client |
| Circle.so community strategy consulting | This is a separate discipline — we set up the tool, not the strategy |
| API token management or rotation | Client responsibility — we guide the process once |
| Ongoing monitoring or alerting | Operator responsibility — we document how to monitor |
| Non-MCP integrations (Zapier, Make, custom APIs) | Out of scope for v1 |
| Destructive operations (delete, archive, moderation) | Not available in v0.3.0 — on the product roadmap |
| Multi-community support | One community per server instance in v0.3.0 |

---

## Support Model

| Tier | Included With | Channel | Response Time |
|------|---------------|---------|---------------|
| **Setup support** | All offers | Email | Within 24 hours (business days) |
| **14-day follow-up** | Setup + Training, Premium | Email | Within 24 hours |
| **30-day follow-up** | Premium, Agency Enablement | Email | Within 24 hours |
| **Priority support** | Agency Enablement, Retainer | Email + optional call | Within 12 hours |
| **Community support** | Everyone (free) | GitHub Issues | Best-effort |

---

## Upsell Path

```
Setup Only ($400–$750)
    │
    ├─→ "Want training too?" → Setup + Training ($750–$1,250)
    │
    └─→ "Need team access?" → Premium + Remote ($1,250–$2,000)
                                    │
                                    └─→ "Want ongoing support?" → Retainer ($200–$500/mo)

Agency Enablement ($2,000–$3,500)
    │
    └─→ "Want priority support as you scale?" → Retainer ($200–$500/mo)
```

**Key upsell moment:** The end of the training session. When the client sees the tools working, they ask about team access and ongoing updates. Be ready with the Premium and Retainer offers.

---

## Recommended First Offer to Lead With

**Setup + Training ($750–$1,250)** for most conversations.

Reasons:
1. Delivers the most value per engagement
2. Creates the strongest client relationship
3. Generates the best testimonials
4. Natural upsell to Premium or Retainer
5. Appropriate complexity for founder-delivered service

For consultants and agencies who express interest in reselling, jump directly to the **Agency Enablement** conversation.

---

## Payment and Terms

| Element | Recommendation |
|---------|----------------|
| **Payment timing** | 100% upfront for Setup and Training offers. 50/50 for Agency Enablement (50% before, 50% after delivery). |
| **Payment method** | Stripe invoice, PayPal, or wire transfer |
| **Refund policy** | Full refund if the server cannot connect to the client's Circle community due to a technical limitation on our side. No refund for client-side issues (invalid token, Circle API restrictions). |
| **Invoicing** | Professional invoice with clear scope description |
