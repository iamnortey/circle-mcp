# Circle.so Subdomain Integration Guide

> **Version:** 1.0.0
> **Last Updated:** 2026-02-16
> **Applies To:** ninobyte.com (Webflow) + learn.ninobyte.com (Circle.so)

---

## 1. Recommended Subdomain

**Subdomain:** `learn.ninobyte.com`

**DNS Configuration:**
- Add a CNAME record pointing `learn.ninobyte.com` to Circle's custom domain target (provided by Circle during setup).
- SSL certificate will be provisioned automatically by Circle once the CNAME propagates.
- Verify configuration in Circle Admin > Settings > Custom Domain.

**Why learn.ninobyte.com:**
- Clear user expectation: "learn" signals the learning environment.
- Separates marketing site (ninobyte.com) from enrolled student experience (learn.ninobyte.com).
- Allows independent analytics tracking for marketing vs. learning engagement.
- Standard industry convention for LMS/course platforms.

---

## 2. CTA Destinations Mapping

This table defines where every CTA on the Webflow site should route.

### CTAs That Route to Circle (learn.ninobyte.com)

| Source Page | CTA Label | Destination URL | Notes |
|------------|-----------|----------------|-------|
| /home-v2 | "Explore Programs" (Hero Primary) | https://ninobyte.com/programs-v2 | Routes to Webflow programs page first |
| /home-v2 | "Enroll Now" (Pricing Section) | https://learn.ninobyte.com/checkout/[plan-id] | Direct to Circle checkout for selected tier |
| /programs-v2 | "Enroll in Level 1" | https://learn.ninobyte.com/checkout/level-1-[tier] | Circle checkout, tier-specific |
| /programs-v2 | "Enroll in Level 2" | https://learn.ninobyte.com/checkout/level-2-[tier] | Circle checkout, tier-specific |
| /program/[slug] | "Enroll Now - Self-Paced" | https://learn.ninobyte.com/checkout/[program]-self-paced | Circle checkout |
| /program/[slug] | "Enroll Now - Live Cohort" | https://learn.ninobyte.com/checkout/[program]-live-cohort | Circle checkout |
| /program/[slug] | "Enroll Now - Premium" | https://learn.ninobyte.com/checkout/[program]-premium | Circle checkout |
| /product/[slug] | "Register for Workshop" | https://learn.ninobyte.com/checkout/[workshop-slug] | Circle checkout |
| /blog/[slug] | "Start Your AI Training" (in-article CTA) | https://learn.ninobyte.com/checkout/level-1-self-paced | Default to Level 1 Self-Paced |
| /success-story/[slug] | "Start Your Journey" | https://learn.ninobyte.com/checkout/level-1-self-paced | Default entry point |

### CTAs That Stay on Webflow (ninobyte.com)

| Source Page | CTA Label | Destination URL | Notes |
|------------|-----------|----------------|-------|
| /home-v2 | "Explore Programs" (Hero) | /programs-v2 | Internal navigation |
| /home-v2 | "Learn More" (sections) | /programs-v2 or /about | Internal navigation |
| /home-v2 | "Contact Us" | /contact-v2 | Internal navigation |
| /programs-v2 | "View Program Details" | /program/[slug] | Internal navigation |
| /programs-v2 | "Contact for Teams" | /contact-v2 | Internal navigation |
| /about | "See Our Programs" | /programs-v2 | Internal navigation |
| /about | "Meet Our Coaches" | /coaches | Internal navigation |
| /coaches | "View Full Profile" | /coach/[slug] | Internal navigation |
| /blog | "Read Article" | /blog/[slug] | Internal navigation |
| /contact-v2 | Form submission | Webflow form handler | Stays on Webflow |
| Navigation | All nav links | Respective Webflow pages | Internal navigation |
| Footer | All footer links | Respective Webflow pages | Internal navigation |

---

## 3. Enrollment Flow

### User Journey: Webflow Landing Page to Circle Enrollment

```
Step 1: DISCOVERY (Webflow)
  User lands on ninobyte.com (organic, paid, or referral)
  └── Views homepage, programs page, or blog post

Step 2: CONSIDERATION (Webflow)
  User clicks "Explore Programs" or navigates to /programs-v2
  └── Reviews program details on /program/[slug]
  └── Reads success stories, checks pricing tiers

Step 3: DECISION (Webflow → Circle Handoff)
  User clicks "Enroll Now - [Tier]" CTA
  └── Redirect to: https://learn.ninobyte.com/checkout/[program]-[tier]
  └── UTM parameters appended automatically (see Section 4)

Step 4: CHECKOUT (Circle)
  User lands on Circle checkout page
  └── Creates Circle account (or logs in if returning)
  └── Completes payment via Circle's payment integration (Stripe)
  └── Receives confirmation email from Circle

Step 5: ONBOARDING (Circle)
  User is redirected to their enrolled space on learn.ninobyte.com
  └── Sees welcome module, orientation content
  └── Joins cohort community space
  └── Begins Level 1 or Level 2 curriculum
```

### Handoff UX Considerations
- **Loading State:** The CTA should open learn.ninobyte.com in the same tab (not a new tab) so the transition feels seamless.
- **Branding Continuity:** Circle community should use the same Ninobyte logo, colors, and fonts as the Webflow site so users feel they are in one ecosystem.
- **Fallback:** If Circle checkout is unavailable, the CTA should link to /contact-v2 with a message: "Enrollment temporarily unavailable. Contact us for assistance."

---

## 4. UTM Parameter Suggestions

Append UTM parameters to all Circle-bound CTAs for attribution tracking.

### UTM Structure

```
https://learn.ninobyte.com/checkout/[program]-[tier]
  ?utm_source=ninobyte_website
  &utm_medium=[page-type]
  &utm_campaign=[program-slug]
  &utm_content=[cta-label-slug]
```

### UTM Parameter Definitions

| Parameter | Value Pattern | Examples |
|-----------|--------------|---------|
| utm_source | `ninobyte_website` (always) | `ninobyte_website` |
| utm_medium | Page type where CTA lives | `homepage`, `programs_listing`, `program_detail`, `blog_post`, `workshop_detail`, `success_story` |
| utm_campaign | Program or product being enrolled in | `level-1-fundamentals`, `level-2-workflow-operator`, `ai-hr-workshop` |
| utm_content | CTA identifier (slugified label) | `hero_enroll_now`, `pricing_self_paced`, `pricing_live_cohort`, `pricing_premium`, `blog_inline_cta`, `bottom_cta` |

### Full URL Examples

**Homepage hero CTA for Level 1 Self-Paced:**
```
https://learn.ninobyte.com/checkout/level-1-self-paced?utm_source=ninobyte_website&utm_medium=homepage&utm_campaign=level-1-fundamentals&utm_content=hero_enroll_now
```

**Program detail page pricing CTA for Level 2 Live Cohort:**
```
https://learn.ninobyte.com/checkout/level-2-live-cohort?utm_source=ninobyte_website&utm_medium=program_detail&utm_campaign=level-2-workflow-operator&utm_content=pricing_live_cohort
```

**Blog post inline CTA:**
```
https://learn.ninobyte.com/checkout/level-1-self-paced?utm_source=ninobyte_website&utm_medium=blog_post&utm_campaign=level-1-fundamentals&utm_content=blog_inline_cta
```

### Tracking Setup
- Configure Google Analytics 4 (GA4) on both ninobyte.com and learn.ninobyte.com with cross-domain tracking enabled.
- In GA4, add learn.ninobyte.com to the cross-domain measurement configuration so sessions are stitched across domains.
- Circle supports UTM parameter capture -- ensure these are mapped to Circle's analytics or CRM fields for enrollment attribution.

---

## 5. Circle Space Structure

### Recommended Community Architecture

```
learn.ninobyte.com (Circle Community)
│
├── WELCOME & ORIENTATION
│   ├── Welcome Space (public to all enrolled members)
│   ├── Getting Started Guide
│   ├── Platform Tour / How to Use Circle
│   └── Community Guidelines
│
├── LEVEL 1: AI WORK FUNDAMENTALS (4 Weeks)
│   ├── Week 1: AI Landscape & Tool Selection
│   ├── Week 2: Prompt Engineering Foundations
│   ├── Week 3: AI Workflow Design
│   ├── Week 4: Portfolio Project Build
│   ├── Level 1 Discussion Forum
│   ├── Level 1 Assignments & Submissions
│   └── Level 1 Office Hours (Live Cohort + Premium only)
│
├── LEVEL 2: AI WORKFLOW OPERATOR (4 Weeks)
│   ├── Week 5: Advanced Prompting & Chaining
│   ├── Week 6: Workflow Automation
│   ├── Week 7: Role-Specific Deep Dive
│   ├── Week 8: Capstone Portfolio Project
│   ├── Level 2 Discussion Forum
│   ├── Level 2 Assignments & Submissions
│   └── Level 2 Office Hours (Live Cohort + Premium only)
│
├── VERTICAL TRACK SPACES
│   ├── GRC / Compliance Track
│   ├── Security Analyst Track
│   ├── Sales / RevOps Track
│   ├── HR / Talent Ops Track
│   ├── Finance / FP&A Track
│   ├── Customer Support Track
│   ├── Marketing / Content Track
│   └── Admin / EA Track
│
├── WORKSHOPS
│   ├── Upcoming Workshops (announcements)
│   └── Workshop Recordings & Materials
│
├── COMMUNITY
│   ├── General Discussion
│   ├── Share Your Work (portfolio showcase)
│   ├── Job Board & Opportunities
│   └── Alumni Network (post-completion)
│
└── RESOURCES
    ├── AI Tool Directory
    ├── Prompt Library
    ├── Template Library
    └── Recommended Reading
```

### Access Control by Tier

| Space | Self-Paced ($497) | Live Cohort ($897) | Premium ($1,497) |
|-------|:-----------------:|:------------------:|:----------------:|
| Welcome & Orientation | Yes | Yes | Yes |
| Level 1 Content | Yes | Yes | Yes |
| Level 2 Content | Yes | Yes | Yes |
| Discussion Forums | Yes | Yes | Yes |
| Live Office Hours | No | Yes | Yes |
| Vertical Track Spaces | No | Yes | Yes |
| 1:1 Coaching Sessions | No | No | Yes |
| Priority Support Channel | No | No | Yes |
| Alumni Network | Yes | Yes | Yes |
| Workshops (if enrolled) | Yes | Yes | Yes |

### Circle Member Tags
Apply tags upon enrollment for segmentation:
- `level-1`, `level-2`
- `self-paced`, `live-cohort`, `premium`
- `track-grc`, `track-security`, `track-sales`, `track-hr`, `track-finance`, `track-support`, `track-marketing`, `track-admin`
- `cohort-2026-q1`, `cohort-2026-q2` (for cohort management)
- `market-usa`, `market-ghana`

---

## 6. Cross-Linking: Circle and Webflow

### Webflow References to Circle

| Location on Webflow | Link Type | Destination |
|---------------------|-----------|-------------|
| All "Enroll" CTAs | Direct link | learn.ninobyte.com/checkout/[product] |
| Navigation bar | "Student Login" link | learn.ninobyte.com/login |
| Footer | "Student Portal" link | learn.ninobyte.com |
| /programs-v2 | "Already enrolled? Log in" | learn.ninobyte.com/login |
| /contact-v2 | "Current students: access your portal" | learn.ninobyte.com |

### Circle References to Webflow

| Location on Circle | Link Type | Destination |
|--------------------|-----------|-------------|
| Circle header/nav | "Main Website" link | ninobyte.com |
| Community sidebar | "View All Programs" | ninobyte.com/programs-v2 |
| Community sidebar | "Blog & Resources" | ninobyte.com/blog |
| Welcome space | "About Ninobyte" | ninobyte.com/about |
| Welcome space | "Meet Your Instructors" | ninobyte.com/coaches |
| Email notifications | Footer link | ninobyte.com |
| Post-completion | "Share Your Story" link | ninobyte.com/contact-v2 (or success story submission form) |

### Branding Consistency Checklist

- [ ] Circle community name: "Ninobyte Learning Community" (or "Ninobyte Academy")
- [ ] Circle logo matches Webflow site logo
- [ ] Circle brand colors match Webflow site colors
- [ ] Circle favicon matches Webflow site favicon
- [ ] Circle email templates use Ninobyte branding
- [ ] Circle custom domain (learn.ninobyte.com) is active and SSL-secured
- [ ] "Powered by Circle" badge placement is acceptable (or hidden on paid plan)

---

*End of Circle Subdomain Integration Guide*
