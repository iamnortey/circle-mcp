# Ideal Customer Profiles and Jobs to Be Done — Circle MCP Server

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Define who this product is for and what outcomes they need

---

## Overview

Circle MCP Server connects Circle.so communities to AI assistants through the Model Context Protocol. This document defines the customer profiles, their jobs to be done, and how the current 16-tool implementation maps to their needs.

---

## ICP 1: Community Consultant

### Profile

- Manages 2-10 client Circle communities
- Charges for community strategy, audits, and optimization
- Uses Circle.so admin access granted by clients
- Technically comfortable but not a developer
- Time is their most valuable resource

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Audit a client community structure in under 10 minutes | `circle_list_spaces`, `circle_list_space_groups`, `circle_get_community` |
| Identify content gaps and neglected discussions | `circle_detect_unanswered_posts`, `circle_list_posts` with search |
| Generate a community health report for a client meeting | `circle_community_health` |
| Find what content exists before recommending new content | `circle_search`, `circle_list_topics` |
| Push recommended content changes to the community | `circle_create_post`, `circle_update_post` |

### Unmet Jobs (Future)

- Compare community health over time (trend data)
- Generate exportable audit reports
- Manage member roles and permissions
- Moderate content (hide, flag, remove)

### Value Statement

> "Audit any Circle community in minutes instead of hours. Get the answers you need for client meetings without clicking through the admin dashboard."

---

## ICP 2: Agency Managing Client Communities

### Profile

- Digital agency with 5-20+ Circle communities under management
- Team of community managers, content strategists, and account managers
- Needs consistency and efficiency across multiple communities
- Cares about repeatable workflows and documented processes

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Run daily health checks across client communities | `circle_community_health` |
| Find and respond to unanswered posts before SLA breach | `circle_detect_unanswered_posts` |
| Create and publish content across client communities | `circle_create_post` |
| Onboard new team members to a client's community structure | `circle_list_spaces`, `circle_list_space_groups` |
| Search for specific content to answer client questions | `circle_search`, `circle_list_posts` |

### Unmet Jobs (Future)

- Multi-community dashboard or comparison
- Bulk content operations
- Notification/webhook integration for real-time alerting
- Member management (invite, ban, role changes)

### Value Statement

> "Operate client communities faster. Health checks, content audits, and content creation — all from one AI conversation instead of switching between admin dashboards."

---

## ICP 3: Community Strategist (In-House)

### Profile

- Dedicated community manager or strategist at a company using Circle.so
- Manages one community, deeply invested in its health and growth
- Reports to marketing, product, or customer success leadership
- Needs to demonstrate community ROI with data

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Start the day with a community health check | `circle_community_health` |
| Find posts that need responses before they go stale | `circle_detect_unanswered_posts` |
| Draft and publish weekly announcements or updates | `circle_create_post` |
| Review recent member activity and growth | `circle_list_members` |
| Understand community taxonomy and content organization | `circle_list_topics`, `circle_list_space_groups` |

### Unmet Jobs (Future)

- Export engagement data for leadership reporting
- Track member engagement over time
- Automate recurring content (weekly digests, check-ins)
- Event and course management

### Value Statement

> "Run daily community operations from Claude. Health checks, content creation, and member insights without leaving your AI workflow."

---

## ICP 4: Advanced Circle Operator

### Profile

- Power user or technical founder with direct Circle.so admin access
- Comfortable with APIs, CLIs, and developer tools
- Wants programmatic access without building custom integrations
- Values safety, transparency, and control

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Query community data via natural language instead of raw API calls | All 16 tools |
| Create content programmatically with safety guardrails | `circle_create_post`, `circle_update_post` |
| Validate community state before and after operations | `circle_community_health`, `circle_list_posts` |
| Integrate Circle data into AI-powered workflows | All tools via MCP protocol |

### Unmet Jobs (Future)

- Webhook subscriptions for event-driven automation
- Bulk operations with confirmation patterns
- Custom tool composition (chain multiple tools)

### Value Statement

> "A production-grade MCP server with 16 validated tools, strict TypeScript, and zero-retry mutation safety. Natural language over your Circle Admin API."

---

## ICP 5: AI-Native Founder

### Profile

- Building a community-powered product on Circle.so
- No dedicated community manager — wearing multiple hats
- Uses AI tools extensively to multiply output
- Needs community to stay active without constant manual effort

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Check if anything needs attention in the community | `circle_community_health`, `circle_detect_unanswered_posts` |
| Create welcome posts and announcements quickly | `circle_create_post` |
| Search for member discussions about specific topics | `circle_search` |
| Get a quick overview of community structure | `circle_list_spaces`, `circle_list_space_groups` |

### Unmet Jobs (Future)

- Automated community nurturing workflows
- Member engagement scoring
- Integration with other AI tools in their stack

### Value Statement

> "Keep your Circle community alive without a dedicated community manager. AI-powered health checks, content creation, and engagement monitoring."

---

## ICP 6: Educator / Cohort Builder

### Profile

- Runs courses, cohorts, or learning communities on Circle.so
- Creates lesson content, manages discussion spaces, tracks student engagement
- May manage multiple cohorts or course iterations
- Values simplicity and reliable content workflows

### Jobs to Be Done

| Job | Current Tool Support |
|-----|---------------------|
| Draft and publish lesson posts from Claude | `circle_create_post` |
| Check for unanswered student questions | `circle_detect_unanswered_posts` |
| Review content in a specific course space | `circle_list_posts` with `space_id` filter |
| Update lesson content or announcements | `circle_update_post` |
| Understand space structure for a course | `circle_list_spaces`, `circle_list_space_groups` |

### Unmet Jobs (Future)

- Course and lesson-specific tools (v0.4 roadmap)
- Event management for live sessions
- Student progress tracking
- Bulk lesson content creation

### Value Statement

> "Draft lessons, check student questions, and manage course spaces — all from Claude without opening the Circle admin dashboard."

---

## Job Priority Matrix

Mapped by frequency and current tool coverage:

| Job | Frequency | Coverage | ICPs |
|-----|-----------|----------|------|
| Community health check | Daily | Full | All |
| Find unanswered posts | Daily | Full | 1, 2, 3, 6 |
| Create/publish content | Weekly | Full | 1, 2, 3, 5, 6 |
| Search community content | Weekly | Full | All |
| Audit community structure | Monthly | Full | 1, 2 |
| Update existing content | Weekly | Full | 3, 6 |
| Review member activity | Weekly | Partial | 3, 5 |
| Generate reports for stakeholders | Monthly | Partial | 2, 3 |
| Multi-community operations | Daily | None | 2 |
| Event/course management | Weekly | None | 6 |
| Member management | Weekly | None | 1, 2, 3 |
| Content moderation | Daily | None | 2, 3 |
