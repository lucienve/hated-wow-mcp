# Project Context: hated-wow-mcp

## Overview

`hated-wow-mcp` is an MCP (Model Context Protocol) server providing 20 tools for World of Warcraft addon development. It supports Retail (Midnight/The War Within), Classic (Mists/Cata/Wrath/TBC), Classic Era (Vanilla), and WoW Forever (Camelot). It supplies in-game Lua API signatures, Blizzard's UI FrameXML and Lua source, CVars, FileDataIDs, texture atlases, Lua linting, XML/TOC validation, and addon scaffolding.

In this repository, it also bundles an Antigravity plugin with rules and 3 specialized skills in `plugins/wow`.

## Upstream Synchronization

- **Upstream Repository:** [RdyGaming/hated-wow-mcp](https://github.com/RdyGaming/hated-wow-mcp)
- **Synchronized Release:** `v0.6.1` (commit `5c427afc4fdb037a199ac3cbb672c5a36aed8864`)

### Merged Upstream Releases & Milestones

1. **v0.5.0 (WoW Forever Flavor Support):**
   - Added `forever` as a first-class flavor with its own bundled API index (`data/api-forever.json`).
   - Fixed UI source loading fallback so unsynced flavors no longer mistakenly receive retail code.
   - Updated interface matching logic in TOC parser (`flavorForInterface`) to properly separate Classic Era (11509) and Forever (16001).
   - Retail interface updated to 120100.
   - Fixed CVar UI usage detection.

2. **v0.6.0 (Client Hardening, Registry Metadata & Per-Client Atlases):**
   - Added overwrite protection to `wow_addon_scaffold` (`overwrite: true` required if destination files exist).
   - Added MCP tool annotations (`readOnlyHint: true` for 19 read-only tools, destructive flags on scaffolding).
   - Sanitized input schemas in `tools/list` to strip `$schema` and `additionalProperties: false` for Gemini-backed client compatibility.
   - Per-client texture atlas indexing (`data/atlas-index-forever.json`, etc.).
   - Added `server.json` and `mcpName` for official MCP Registry publishing.
   - Expanded test suite to 111 smoke and protocol tests.

3. **v0.6.1 (Path Suggestions for UI Source):**
   - Enhanced `wow_ui_read_file` to suggest up to 5 likely candidate file paths when an exact match fails (case differences, suffixes, same filename).

## Local Customizations

- **Antigravity Plugin (`plugins/wow`):**
  - Registered MCP server configuration in `plugins/wow/mcp_config.json`.
  - Addon development rules in `plugins/wow/rules/AGENTS.md`.
  - Three on-demand skills: `wow-addon-feature`, `wow-addon-troubleshoot`, `wow-addon-scaffold`.
- **Dependency Tracking:**
  - Modernized dependency versions (`fast-xml-parser` v5, `zod` v4, `@types/node` v26, `typescript` v7, `tsx` v4).
  - Configured Dependabot and lifecycle scripts allowlisting (`allowScripts` in `package.json`).
