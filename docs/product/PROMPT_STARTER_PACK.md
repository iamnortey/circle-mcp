# Prompt Starter Pack — Circle MCP

> **Version:** 1.0.0
> **Last Updated:** 2026-03-13
> **Purpose:** Ready-to-use prompts for common Circle community operations via Claude

---

## How to Use This Pack

Copy any prompt below and paste it into Claude Desktop (or any MCP client connected to Circle MCP Server). Claude will translate your natural language into the appropriate Circle API calls.

These prompts are organized by workflow. Each prompt is tested against the 16 tools available in v0.3.0.

---

## Daily Operations

### Morning Health Check

```
Give me a community health snapshot. How many members, posts, and spaces
do I have? Which spaces have the most posts?
```

**Tools used:** `circle_community_health`

### Find Unanswered Questions

```
Are there any unanswered posts in my community? Show me posts with zero
comments across all spaces.
```

**Tools used:** `circle_detect_unanswered_posts`

### Unanswered Posts in a Specific Space

```
Check for unanswered posts in space [SPACE_ID]. Are there questions
that need a response?
```

**Tools used:** `circle_detect_unanswered_posts` with `space_id` filter

### Recent Activity Check

```
Show me the 10 most recent posts in my community, sorted by latest.
```

**Tools used:** `circle_list_posts` with `sort: latest`

---

## Community Audit

### Full Space Inventory

```
List all spaces in my community. Include their names, types, and URLs.
I want to see the complete structure.
```

**Tools used:** `circle_list_spaces`

### Space Group Overview

```
Show me how spaces are organized into groups. List each group and
which spaces belong to it.
```

**Tools used:** `circle_list_space_groups`

### Content Taxonomy

```
List all topics (tags) in my community. How many posts does each
topic have?
```

**Tools used:** `circle_list_topics`

### Community Metadata

```
Show me the community settings — name, URL, slug, and any
configuration details.
```

**Tools used:** `circle_get_community`

### Member Overview

```
How many members do I have? Show me the most recent members
who joined, with their names and join dates.
```

**Tools used:** `circle_list_members` with `sort: latest`

---

## Content Search and Discovery

### Search for a Topic

```
Search my community for posts and discussions about "onboarding".
What exists already?
```

**Tools used:** `circle_search`

### Find Posts by Keyword

```
Find all published posts that mention "pricing" in their content.
```

**Tools used:** `circle_list_posts` with `search_text`

### Review a Specific Post

```
Show me the full content of post [POST_ID], including the complete
body text.
```

**Tools used:** `circle_get_post`

### List Comments on a Post

```
Show me all comments on post [POST_ID]. Who responded and what
did they say?
```

**Tools used:** `circle_list_comments` with `post_id`

---

## Content Creation

### Draft an Announcement

```
Create a draft post in space [SPACE_ID] titled "Weekly Community Update"
with this body:

<p>Hi everyone! Here's what happened this week:</p>
<ul>
  <li>We launched our new AI integration</li>
  <li>Three new members joined</li>
  <li>The FAQ space was reorganized</li>
</ul>
<p>Let us know what you'd like to see next week!</p>

Save it as a draft so I can review before publishing.
```

**Tools used:** `circle_create_post` with `status: draft`

### Publish a Welcome Post

```
Create a published post in space [SPACE_ID] titled "Welcome to Our
Community!" with this body:

<p>Welcome! We're glad you're here.</p>
<p>Here's how to get started:</p>
<ol>
  <li>Introduce yourself in the Introductions space</li>
  <li>Check out our Getting Started guide</li>
  <li>Ask questions anytime — we're here to help</li>
</ol>

Publish it immediately and enable comments.
```

**Tools used:** `circle_create_post` with `status: published`

### Update an Existing Post

```
Update post [POST_ID]. Change the title to "Updated: Community Guidelines
v2" and add this paragraph to the body:

<p><strong>New for March 2026:</strong> We've updated our guidelines to
include AI tool usage policies. Please review the changes.</p>
```

**Tools used:** `circle_update_post`

---

## Agency and Consultant Workflows

### Client Community Audit (5-Minute Version)

Run these prompts in sequence for a rapid community assessment:

```
1. Give me a community health snapshot.
2. List all spaces and space groups.
3. Are there any unanswered posts?
4. Show me the 20 most recent posts sorted by latest.
5. List all topics in the community.
```

**Tools used:** `circle_community_health`, `circle_list_spaces`, `circle_list_space_groups`, `circle_detect_unanswered_posts`, `circle_list_posts`, `circle_list_topics`

### Content Gap Analysis

```
I'm auditing this community for content gaps. Please:
1. List all spaces
2. For each space, check if there are unanswered posts
3. Show me spaces that have posts with zero comments

I want to know where members are asking questions but not getting answers.
```

**Tools used:** `circle_list_spaces`, `circle_detect_unanswered_posts`

### Pre-Meeting Community Summary

```
Give me a summary I can present in a client meeting:
- Community name and URL
- Total members, posts, and spaces
- Any unanswered posts that need attention
- The 5 most recent posts
```

**Tools used:** `circle_community_health`, `circle_detect_unanswered_posts`, `circle_list_posts`

---

## Educator Workflows

### Check for Student Questions

```
Are there unanswered posts in space [COURSE_SPACE_ID]? I need to
respond to any student questions before the next session.
```

**Tools used:** `circle_detect_unanswered_posts` with `space_id`

### Post a Lesson

```
Create a published post in space [SPACE_ID] titled "Week 3: Advanced
Techniques" with this body:

<h2>Learning Objectives</h2>
<ul>
  <li>Understand advanced configuration patterns</li>
  <li>Practice with real-world examples</li>
  <li>Complete the hands-on exercise</li>
</ul>
<h2>Resources</h2>
<p>Review the attached materials before our live session on Thursday.</p>

Enable comments so students can ask questions.
```

**Tools used:** `circle_create_post`

### Update Course Content

```
Update post [POST_ID] to fix a typo. Change the body to include this
corrected paragraph:

<p>The deadline for Exercise 3 is <strong>March 20</strong>, not March 15
as previously stated. Please plan accordingly.</p>
```

**Tools used:** `circle_update_post`

---

## Tips for Better Prompts

1. **Be specific with IDs.** When you know a space or post ID, include it. Claude can look them up, but providing IDs is faster.

2. **Use HTML for post bodies.** Circle posts use HTML. Wrap your content in `<p>`, `<ul>`, `<h2>`, etc. for proper formatting.

3. **Start with drafts.** When creating content, use `status: draft` so you can review before publishing.

4. **Chain commands.** Claude can run multiple tools in one conversation. Ask for a health check AND a list of unanswered posts in the same message.

5. **Ask about limitations.** If you are unsure whether a tool exists for something, ask. Claude will tell you honestly what is and is not available.

---

## Tool Quick Reference

| What You Want to Do | Prompt Shortcut |
|---------------------|-----------------|
| Community health | "Give me a health snapshot" |
| List spaces | "Show me all spaces" |
| Unanswered posts | "Find unanswered posts" |
| Search content | "Search for [keyword]" |
| Create a post | "Create a [draft/published] post in space [ID]" |
| Update a post | "Update post [ID] with..." |
| List members | "Show me recent members" |
| Space structure | "How are spaces organized?" |
| Content topics | "What topics exist?" |
| Post details | "Show me post [ID]" |
| Comments on a post | "Show comments on post [ID]" |
