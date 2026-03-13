# Circle MCP Server — Discovery Report

> **Generated:** 2026-03-11
> **Phase:** Prompt 1/6 — Workspace Setup & Discovery
> **Status:** Complete

---

## 1. Scan Summary

A broad discovery sweep was performed across the local Mac filesystem to locate any existing Circle.so, MCP, or OpenAPI artifacts that could accelerate the Circle MCP server build. The scan covered Desktop, Downloads, Documents, Developer, and Library/Application Support directories.

**Scan method:** Spotlight (`mdfind`) + recursive directory listing + targeted `grep` for Circle API URLs and OpenAPI filenames.

---

## 2. Circle-Specific Content Found

| # | File | Location | Size | Relevance |
|---|------|----------|------|-----------|
| 1 | `Ninobyte_Circle_Community_Copy_Pack_v2.md` | `~/Desktop/Ninobyte Brand/` | 20 KB | HIGH — Community name, tagline, space names, content structure |
| 2 | `04_Circle_Landing_Page_Copy_Strategy.md` | `~/Desktop/Ninobyte Brand/` | 12.6 KB | MEDIUM — Landing page strategy, 3-surface architecture |
| 3 | `12_Circle_Landing_Page_Final_Web_Copy.md` | `~/Desktop/Ninobyte Brand/` | 12.5 KB | MEDIUM — Final web copy for Circle landing |
| 4 | `CIRCLE_SUBDOMAIN_INTEGRATION.md` | `~/Desktop/NINOBYTE_MASTER/.../03_circle_handoff/` | 11.7 KB | HIGH — Subdomain setup, confirms ninobyte.circle.so is live |
| 5 | `Circle Cover Pages.pptx` | `~/Desktop/Ninobyte Brand/` | 1.8 MB | LOW — Visual assets only |

**Key insight:** These files confirm an active Circle community at `ninobyte.circle.so` under the `learn.ninobyte.com` subdomain. The copy pack reveals community structure (space names, descriptions) which will be useful for validating MCP tool output against real data.

All high/medium relevance files have been copied into `docs/research/`.

---

## 3. Circle Desktop App Findings

| Item | Detail |
|------|--------|
| App installed | YES — `~/Library/Application Support/circle-app/` |
| Runtime | Electron 35.4.0 |
| Active community | `ninobyte.circle.so` (confirmed via IndexedDB) |
| Automation support | NONE — no AppleScript dictionary, no Shortcuts, no plugin system |
| URL scheme | `circle://` (basic, no documented deep links) |
| Developer docs | NONE |

**Conclusion:** The Circle desktop app is not a viable integration path. API-first approach confirmed.

---

## 4. Circle Banner/Visual Assets

| File | Location | Size |
|------|----------|------|
| 5 PNG templates + 1 ZIP | `~/Downloads/Circle Space Banner Templates/` | ~10 MB total |

**Relevance:** LOW — Visual assets only, not needed for MCP server. Not copied into project.

---

## 5. OpenAPI / Swagger Specifications

| Search | Result |
|--------|--------|
| Local OpenAPI specs (`swagger.yaml`, `openapi.json`, etc.) | NONE found for Circle |
| Circle API URL references (`api.circle.so`, `app.circle.so/api`) | NONE in local files |
| Postman/Insomnia collections | NONE found |

**Action required:** Download the Circle Admin API V2 OpenAPI spec from `https://api-headless.circle.so/api/admin/v2/swagger.yaml` during Prompt 2/6.

---

## 6. MCP Reference Projects Found

### 6a. Existing MCP Servers (Local)

| Project | Path | Language | Key Dependencies |
|---------|------|----------|-----------------|
| `gluestack-mcp` | `~/Developer/mcp-servers/gluestack-mcp/` | TypeScript | `@modelcontextprotocol/sdk@^1.11.5`, `zod@^3.25.20` |
| `playwright-mcp` | `~/Developer/mcp-servers/playwright-mcp/` | TypeScript | MCP SDK |
| `ninobyte-airgap` | `~/Developer/ninobyte/products/mcp-servers/ninobyte-airgap/` | Python | Custom security-focused architecture |

**Key pattern from `gluestack-mcp`:**
- ES module (`"type": "module"`)
- Uses `@modelcontextprotocol/sdk` + `zod` (matches our planned stack)
- Good reference for `package.json` and project structure

### 6b. Framer MCP Setup Documentation

| File | Copied To | Use |
|------|-----------|-----|
| `01_Framer_MCP_Validation.md` | `docs/evidence/framer_mcp_validation_template.md` | Template for Circle MCP validation report |
| `02_Framer_MCP_Setup_Runbook.md` | `docs/evidence/framer_mcp_runbook_template.md` | Template for Circle MCP setup runbook |
| `03_Framer_MCP_Status.md` | `docs/evidence/framer_mcp_status_template.md` | Template for Circle MCP status tracking |

### 6c. Other MCP References

| Item | Path | Relevance |
|------|------|-----------|
| VAPI MCP docs | `~/Developer/ninolex-core/docs/vapi_mcp/` | LOW — different service, but shows MCP integration patterns |

---

## 7. Claude Desktop Configuration

| Item | Detail |
|------|--------|
| Config location | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Existing MCP servers | filesystem, github, brave-search, memory, sanity, supabase, sequential-thinking, figma, gluestack-mcp, playwright |
| Config format confirmed | JSON with `mcpServers` object, each entry has `command`, `args`, `env` |

**Action required:** Add `circle-mcp` entry to this config after server is built (Prompt 5/6).

---

## 8. Development Environment

| Tool | Version | Status |
|------|---------|--------|
| Node.js | 24.7.0 | ✅ Ready |
| npm | 11.5.1 | ✅ Ready |
| TypeScript | 5.9.2 | ✅ Ready |
| pnpm | 10.26.2 | ✅ Available |
| bun | Not installed | N/A (not needed) |
| Git | Installed | ✅ Ready |

---

## 9. Items NOT Found (Confirmed Absent)

| Item | Status | Required By |
|------|--------|-------------|
| Circle OpenAPI spec (local copy) | ❌ MISSING | Prompt 2/6 |
| Circle API response samples | ❌ MISSING | Prompt 2/6 |
| Circle API token / credentials | ❌ MISSING | Human prerequisite |
| Circle community ID | ❌ MISSING | Human prerequisite |
| Circle TipTap format examples | ❌ MISSING | Prompt 3/6 |

---

## 10. Recommendations for Next Steps

1. **Human action required:** Generate a Circle API token from the Circle admin dashboard and provide it for `.env` configuration
2. **Human action required:** Identify the Circle community ID from the dashboard
3. **Prompt 2/6 scope:** Download OpenAPI spec, make manual API calls to capture 5–6 real response samples, create API contract files
4. **Pattern reuse:** Use `gluestack-mcp` as structural reference for TypeScript MCP server setup
5. **Documentation reuse:** Adapt Framer MCP validation/runbook/status templates for Circle MCP equivalents
