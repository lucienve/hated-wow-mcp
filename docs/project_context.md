# Project Context: hated-wow-mcp

## Overview

`hated-wow-mcp` is an MCP (Model Context Protocol) server providing 27 tools for
World of Warcraft addon development and asset extraction. It supports Retail
(Midnight/The War Within), Classic (Mists/Cata/Wrath/TBC), Classic Era
(Vanilla), and WoW Forever (Camelot). It supplies in-game Lua API signatures,
Blizzard's UI FrameXML and Lua source, CVars, FileDataIDs, texture atlases, Lua
linting, XML/TOC validation, addon scaffolding, and headless export of
creatures, NPCs, props, buildings, and zone terrain from a local install to
glTF/OBJ with PNG textures.

In this repository, it also bundles an Antigravity plugin with rules and 3
specialized skills in `plugins/wow`.

## Upstream Synchronization

- **Upstream Repository:** [RdyGaming/hated-wow-mcp](https://github.com/RdyGaming/hated-wow-mcp)
- **Synchronized Release:** `v0.8.0` (commit `85fbc1249b6b7a701b2289c033878b668f44ff5c`, upstream tag `v0.8.0` at `b0893aac3bd42d82ee85733677fec974fb696f71`)

### Merged Upstream Releases & Milestones

1. **v0.5.0 (WoW Forever Flavor Support):**
   - Added `forever` as a first-class flavor with its own bundled API index
     (`data/api-forever.json`).
   - Fixed UI source loading fallback so unsynced flavors no longer mistakenly
     receive retail code.
   - Updated interface matching logic in TOC parser (`flavorForInterface`) to
     properly separate Classic Era (11509) and Forever (16001).
   - Retail interface updated to 120100.
   - Fixed CVar UI usage detection.

2. **v0.6.0 (Client Hardening, Registry Metadata & Per-Client Atlases):**
   - Added overwrite protection to `wow_addon_scaffold` (`overwrite: true`
     required if destination files exist).
   - Added MCP tool annotations (`readOnlyHint: true` for read-only tools,
     destructive flags on scaffolding and exporters).
   - Sanitized input schemas in `tools/list` to strip `$schema` and
     `additionalProperties: false` for Gemini-backed client compatibility.
   - Per-client texture atlas indexing (`data/atlas-index-forever.json`, etc.).
   - Added `server.json` and `mcpName` for official MCP Registry publishing.
   - Expanded test suite to 111 smoke and protocol tests.

3. **v0.6.1 (Path Suggestions for UI Source):**
   - Enhanced `wow_ui_read_file` to suggest up to 5 likely candidate file paths
     when an exact match fails (case differences, suffixes, same filename).

4. **v0.7.0 (Headless 3D Model & Map Terrain Export):**
   - Ported wow.export's CASC storage reader, M2/WMO exporters, ADT/WDT terrain
     loaders, and BLP texture decoders into headless Node scripts
     (`src/wowexport/**`).
   - Added 7 export and lookup tools: `wow_model_search`, `wow_model_export`,
     `wow_creature_search`, `wow_creature_export`, `wow_texture_export`,
     `wow_map_search`, and `wow_map_export`.
   - Added `sync-model-data` (`src/sync/model-data.ts`) to fetch public WoWDBDefs
     table column definitions and TACTKeys encryption keys.
   - Added `scripts/copy-lib.mjs` to copy CommonJS exporter modules and licenses
     to `dist/wowexport/`.

5. **v0.8.0 (NPC Armor/Gear Geosets & Released WoW Forever Client):**
   - NPC creature export now includes armor geosets and attachment bones for
     helms, shoulders, and weapons.
   - Added support for released WoW Forever client under `_forever_` / product
     `wowf` (replacing beta `_classic_beta_` / `wow_classic_beta`), with full
     global, event, and CVar API lists.

## Local Customizations

- **Antigravity Plugin (`plugins/wow`):**
  - Registered MCP server configuration in `plugins/wow/mcp_config.json`.
  - Conditional addon development rules in
    `plugins/wow/rules/wow_addon_development.md` using `trigger: model_decision`
    frontmatter for progressive disclosure (preventing rule pollution in
    non-WoW projects).
  - Three on-demand skills: `wow-addon-feature`, `wow-addon-troubleshoot`,
    `wow-addon-scaffold`.
- **Dependency Tracking:**
  - Modernized dependency versions (`fast-xml-parser` v5, `zod` v4,
    `@types/node` v26, `typescript` v7, `tsx` v4, `express` v5).
  - Configured Dependabot and lifecycle scripts allowlisting (`allowScripts` in
    `package.json`).
  - Added `"types": ["node"]` in `tsconfig.json` for TypeScript 7 +
    `@types/node` 26 compatibility.
