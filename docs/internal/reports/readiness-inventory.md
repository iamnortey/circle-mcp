# Circle MCP Server — Readiness Inventory

> **Generated:** 2026-03-11
> **Phase:** Prompt 1/6 — Workspace Setup & Discovery
> **Purpose:** Track what's present, partially available, or missing before starting implementation

---

## Legend

| Status | Meaning |
|--------|---------|
| ✅ PRESENT | Available locally, ready to use |
| 🟡 PARTIAL | Exists but needs work or is incomplete |
| ❌ MISSING | Not found, must be obtained before proceeding |

---

## A. Infrastructure & Tooling

| Item | Status | Detail |
|------|--------|--------|
| Node.js (>= 18) | ✅ PRESENT | v24.7.0 |
| npm | ✅ PRESENT | v11.5.1 |
| TypeScript | ✅ PRESENT | v5.9.2 |
| pnpm (optional) | ✅ PRESENT | v10.26.2 |
| Git | ✅ PRESENT | Installed |
| Project directory structure | ✅ PRESENT | `~/Developer/circle-mcp/` with full subdirectory tree |
| Claude Desktop | ✅ PRESENT | Installed with existing MCP server configs |

---

## B. Circle API Prerequisites

| Item | Status | Detail | Owner |
|------|--------|--------|-------|
| Circle Business plan (or higher) | 🟡 PARTIAL | Community at ninobyte.circle.so exists, plan tier unconfirmed | Human |
| Circle API token | ❌ MISSING | Not found in any local `.env` files | Human |
| Circle community ID | ❌ MISSING | Needed for API calls, obtained from dashboard | Human |
| OpenAPI V2 spec (local copy) | ❌ MISSING | Must download from `api-headless.circle.so` | Prompt 2/6 |
| API response samples (5–6 endpoints) | ❌ MISSING | Must be captured via manual `curl` calls | Prompt 2/6 |
| TipTap format examples | ❌ MISSING | Must be extracted from real post bodies | Prompt 3/6 |

---

## C. Reference Materials & Patterns

| Item | Status | Location in Project |
|------|--------|-------------------|
| Circle community copy pack | ✅ PRESENT | `docs/research/circle_community_copy_pack.md` |
| Circle landing page strategy | ✅ PRESENT | `docs/research/circle_landing_page_strategy.md` |
| Circle landing page final copy | ✅ PRESENT | `docs/research/circle_landing_page_copy.md` |
| Circle subdomain integration guide | ✅ PRESENT | `docs/research/circle_subdomain_integration.md` |
| Framer MCP validation template | ✅ PRESENT | `docs/evidence/framer_mcp_validation_template.md` |
| Framer MCP runbook template | ✅ PRESENT | `docs/evidence/framer_mcp_runbook_template.md` |
| Framer MCP status template | ✅ PRESENT | `docs/evidence/framer_mcp_status_template.md` |

---

## D. MCP SDK & Pattern References

| Item | Status | Detail |
|------|--------|--------|
| `@modelcontextprotocol/sdk` reference | ✅ PRESENT | Used by `gluestack-mcp` at `^1.11.5` |
| `zod` schema reference | ✅ PRESENT | Used by `gluestack-mcp` at `^3.25.20` |
| Working MCP `package.json` pattern | ✅ PRESENT | `~/Developer/mcp-servers/gluestack-mcp/package.json` |
| Claude Desktop MCP config format | ✅ PRESENT | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| TypeScript MCP server structure | ✅ PRESENT | `gluestack-mcp` and `playwright-mcp` as references |
| Python MCP server structure | ✅ PRESENT | `ninobyte-airgap` as alternative reference |

---

## E. Knowledge & Documentation

| Item | Status | Detail |
|------|--------|--------|
| Circle Admin API V2 docs | 🟡 PARTIAL | Studied in feasibility phase, not saved locally yet |
| Circle rate limit rules | ✅ PRESENT | Documented in feasibility study (2K/5min, monthly quotas) |
| Circle auth model | ✅ PRESENT | Documented (Bearer token, JWT for member API) |
| MCP protocol specification | 🟡 PARTIAL | Reviewed, will be re-fetched during implementation |
| TypeScript SDK README | 🟡 PARTIAL | Will be fetched fresh during Prompt 2/6 |

---

## F. Prompt 2/6 Blockers

These items MUST be resolved before Prompt 2/6 can proceed:

| # | Blocker | Resolution | Owner |
|---|---------|------------|-------|
| 1 | Circle API token | Generate from Circle admin dashboard → Settings → API | Human |
| 2 | Circle community ID | Copy from Circle admin dashboard URL or API settings | Human |
| 3 | Confirm Business plan or higher | Verify from Circle billing settings | Human |

---

## G. Readiness Score

| Category | Score | Notes |
|----------|-------|-------|
| Local tooling | 7/7 | All dev tools present and verified |
| Circle API access | 0/3 | Token, community ID, and plan tier unconfirmed |
| Reference materials | 7/7 | All content files and MCP patterns collected |
| OpenAPI contract | 0/1 | Must be downloaded |
| API samples | 0/1 | Must be captured |
| **Overall** | **14/19 (74%)** | **Blocked on 3 human prerequisites + 2 download tasks** |

---

## H. Summary

The workspace is fully scaffolded and all locally available reference materials have been collected. The development environment is verified and ready.

**Three human actions are required before Prompt 2/6:**
1. Provide a Circle API token
2. Provide the Circle community ID
3. Confirm the Circle.so plan tier supports API access (Business or higher)

Once those are provided, Prompt 2/6 can proceed with downloading the OpenAPI spec and capturing real API response samples.
