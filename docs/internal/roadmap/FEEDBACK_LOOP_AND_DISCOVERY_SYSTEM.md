# Feedback Loop and Discovery System — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Define how customer learning converts into product decisions
> **Principle:** Every client interaction is a data point. The system captures, organizes, and acts on those data points.

---

## Feedback Philosophy

At Stage 1, the founder IS the feedback system. There is no analytics dashboard, no NPS survey tool, no product analytics pipeline. The feedback loop is:

1. **Talk to clients** — every session, every demo, every support interaction
2. **Write down what they say** — specific words, specific requests, specific friction points
3. **Count the patterns** — when 3+ people say the same thing, it becomes a roadmap candidate
4. **Build what earned it** — features validated by revenue signal, not speculation

This system works because the founder delivers every engagement. There is zero distance between the customer and the decision-maker.

---

## 1. Feedback Collection Points

### During Paid Sessions

| Moment | What to Capture | How |
|--------|----------------|-----|
| **Setup phase** | Where did they get stuck? What was confusing? How long did each step take? | Note during session |
| **Training phase** | Which prompts excited them? Which ones confused them? What did they ask to do that the tool cannot? | Note during session |
| **End of session** | "What would make this more useful for you?" (mandatory close question) | Ask directly, write down verbatim |
| **24-hour follow-up** | "Now that you have used it for a day, what is working and what is not?" | Email follow-up |

### During Demos

| Moment | What to Capture |
|--------|----------------|
| **Initial reaction** | What feature caught their attention first? |
| **Questions asked** | What did they ask about? (Questions reveal priorities.) |
| **Objections raised** | Price? Complexity? Missing features? Platform concerns? |
| **Decision** | Did they convert? If not, why not? |

### From Self-Serve Users

| Signal | What It Tells You |
|--------|-------------------|
| GitHub issue opened | Specific bug or feature request — highest signal from self-serve users |
| GitHub star | Awareness and interest, low signal for specific needs |
| Smithery install | Someone tried it — check if they followed up with an issue or question |
| Support email | Direct friction point — capture verbatim |

### From Outreach Responses

| Signal | What It Tells You |
|--------|-------------------|
| "Not interested" | Wrong segment, wrong timing, or wrong message |
| "Interesting, but..." | The "but" is the insight. Capture the objection. |
| "How does this handle X?" | X is a feature they need. Log it. |
| "We already use Y for this" | Y is a competitor or alternative. Understand the comparison. |

---

## 2. Feedback Storage

### The Feedback Log (Spreadsheet)

A single spreadsheet with one row per feedback item.

| Column | Purpose |
|--------|---------|
| **Date** | When the feedback was received |
| **Source** | Client name, prospect name, GitHub user, or "anonymous" |
| **Channel** | Session, demo, email, GitHub issue, outreach response |
| **Segment** | Consultant, agency, operator, founder, educator |
| **Type** | Feature request, bug report, friction point, praise, objection |
| **Verbatim** | Exact words they used (not your interpretation) |
| **Category** | Setup, tools, prompts, pricing, docs, performance, missing feature |
| **Frequency** | How many times this exact feedback has been heard (update as it recurs) |
| **Action** | None, Investigate, Build, Fix, Document |
| **Status** | Open, In Progress, Done, Deferred |

### Why a Spreadsheet

- Zero setup cost
- Searchable and sortable
- Exportable if you ever need to share with a co-founder or advisor
- No tool to maintain or pay for
- Good enough for < 50 feedback items (which covers all of Stage 1)

### When to Graduate from a Spreadsheet

When the feedback log exceeds 100 items and pattern detection becomes manual. At that point, consider a simple tool like Notion, Airtable, or Linear. Not before.

---

## 3. Pattern Detection

### The 3x Rule

A feedback item becomes a roadmap candidate when **3 or more independent sources** report the same need.

| Count | Status | Action |
|-------|--------|--------|
| 1 | Noted | Log it. Do nothing. |
| 2 | Interesting | Log it. Watch for more. |
| 3+ | Validated | Add to feature prioritization. Evaluate effort and leverage. |

### Pattern Categories

| Pattern | Example | Implication |
|---------|---------|-------------|
| **Missing tool** | "Can it show events?" / "I need course data" | Feature gap — evaluate for v0.4/v0.5 |
| **Setup friction** | "The build step confused me" / "I could not find my token" | Onboarding improvement — high leverage |
| **Pricing concern** | "Is there a cheaper option?" / "Can I just pay for setup?" | Offer ladder adjustment or new tier |
| **Workflow gap** | "I want to do X then Y automatically" | Workflow pattern — document as prompt, not a feature |
| **Performance issue** | "It was slow with 500 posts" / "Timed out on member list" | Pagination or performance fix needed |
| **Competitor mention** | "We use Zapier for community ops" | Competitive positioning insight |

### Weekly Pattern Review

Every Friday during Stage 1 (15 minutes):

1. Open the feedback log
2. Sort by Category
3. Count frequency for each item
4. Highlight any item that hit 3+ occurrences this week
5. Update the FEATURE_PRIORITIZATION doc if new candidates emerged

---

## 4. Feedback → Product Decision Pipeline

```
Client says something
        │
        ▼
Log in feedback spreadsheet (verbatim)
        │
        ▼
Categorize: feature request / bug / friction / praise / objection
        │
        ▼
Count: has this been heard 3+ times?
        │
   ┌────┴────┐
   No        Yes
   │         │
   Wait      ▼
             Evaluate: effort, leverage, revenue signal
                      │
                ┌─────┴─────┐
                Low effort   High effort
                │            │
                ▼            ▼
             Build in     Stage in
             next sprint  FEATURE_PRIORITIZATION
                │            │
                └─────┬──────┘
                      ▼
                Document decision in
                FEATURE_PRIORITIZATION decision log
```

### Decision Criteria

| Factor | Question |
|--------|----------|
| Revenue signal | Did paying clients request this, or free users? |
| Effort | Can this be built in < 8 hours? |
| Leverage | Does it unlock a new segment or reduce delivery cost? |
| Urgency | Is it blocking a sale or causing a client to churn? |
| Alignment | Does it fit the current maturity stage? |

### What to Do with Praise

Praise is feedback too. When a client says "This is exactly what I needed" or "The health check saved me an hour" — capture it:

1. Log it in the feedback spreadsheet (Type: praise)
2. Ask if they would share it as a testimonial
3. Use the specific language in future outreach and announcements
4. Note which tool or workflow generated the praise — double down on those

---

## 5. Discovery System

### What "Discovery" Means at Stage 1

Discovery is not user research at scale. It is structured curiosity during every client interaction.

### Discovery Questions (ask during every session)

| Question | What It Reveals |
|----------|----------------|
| "What does your typical Circle workflow look like today?" | Their current process — where the tool fits and where it does not |
| "What takes the most time in your community management?" | Pain points the tool might address |
| "If this tool could do one more thing, what would it be?" | Feature demand signal |
| "How would you explain this tool to a colleague?" | Their mental model — is it what you intended? |
| "What would make you recommend this to someone else?" | Referral triggers and advocacy signals |

### Discovery Signals from Non-Verbal Cues

| Signal | Meaning |
|--------|---------|
| They lean in during health check demo | Health check is the hook |
| They ask "can it do X?" repeatedly | Feature gap or workflow gap |
| They go quiet after pricing discussion | Price resistance — probe gently |
| They immediately share with a colleague | Advocacy signal — ask for referral |
| They try a prompt that is not in the starter pack | Advanced user — learn from their creativity |

---

## 6. Feedback-Revenue Alignment

### The Core Question

> "Is the thing clients are asking for also the thing that would make more people pay?"

| Feedback Type | Revenue Impact | Action |
|---------------|----------------|--------|
| Feature that unblocks a sale | Direct revenue | Build it (if effort is reasonable) |
| Feature that reduces delivery cost | Indirect revenue (margin improvement) | Build it when 3+ sessions confirm |
| Feature that is "nice to have" | No revenue impact | Defer — nice-to-have does not pay bills |
| Feature that requires a new segment | Uncertain revenue | Stage — investigate before building |

### Revenue Attribution

For the first 10 clients, track:

| Client | Segment | Offer | Revenue | Top Feature Used | Top Feature Requested |
|--------|---------|-------|---------|-----------------|----------------------|
| [name] | Consultant | Setup + Training | $1,000 | Health check | Event tools |
| [name] | Agency | Premium + Remote | $1,500 | Unanswered posts | Multi-community |

This table directly connects revenue to features — both existing (what they use) and requested (what they want). It is the most honest product roadmap input available.

---

## 7. Anti-Patterns to Avoid

| Anti-Pattern | Why It Is Dangerous |
|-------------|---------------------|
| **Building for one loud client** | One person's urgent need is not the market's need. Wait for the 3x rule. |
| **Ignoring feedback that contradicts your vision** | If clients consistently want something you did not plan, listen. The market does not care about your roadmap. |
| **Collecting feedback without reviewing it** | A full spreadsheet nobody reads is worse than no spreadsheet. Review weekly. |
| **Treating all feedback equally** | Paying clients > free users > prospects > random GitHub users. Weight by commitment level. |
| **Surveying instead of conversing** | NPS surveys and Google Forms lose nuance. Direct conversation captures what surveys miss. |
| **Building a feedback tool before having feedback** | The spreadsheet works until it does not. Do not buy Linear for 5 clients. |

---

## 8. Feedback Cadence

| Timeframe | Activity | Time |
|-----------|----------|------|
| Every session | Ask mandatory close question + log feedback | 5 min |
| 24 hours after session | Send follow-up email | 5 min |
| Weekly (Friday) | Review feedback log, count patterns, update priorities | 15 min |
| Monthly | Feedback retrospective: what patterns emerged? What changed? | 30 min |
| After 5 paid clients | Comprehensive review: what works, what does not, what to change | 1 hour |
| **Total weekly overhead** | | **~30 minutes** |

This is sustainable. If the feedback system takes more time than delivering the service, something is wrong.
