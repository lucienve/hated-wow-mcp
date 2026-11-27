# Project Context: hated-wow-mcp

## Overview

`hated-wow-mcp` is an MCP (Model Context Protocol) server providing 28 tools for
World of Warcraft addon development and asset extraction. It supports Retail
(Midnight/The War Within), Classic (Mists/Cata/Wrath/TBC), Classic Era
(Vanilla), and WoW Forever (Camelot). It supplies in-game Lua API signatures,
Blizzard's UI FrameXML and Lua source, CVars, FileDataIDs, texture atlases, Lua
linting, XML/TOC validation, addon scaffolding, local item and hotfix cache
(`DBCache.bin`) search, and headless export of creatures, NPCs, props,
buildings, and zone terrain from a local install to glTF/OBJ with PNG textures.

In this repository, it also bundles an Antigravity plugin with rules and 4
specialized skills in `plugins/wow`.

## Upstream Synchronization

- **Upstream Repository:**
  [RdyGaming/hated-wow-mcp](https://github.com/RdyGaming/hated-wow-mcp)
- **Synchronized Release:** `v0.9.0` (commit
  `41d7579a4eea3a8e566c707226ca00bdd6e2bcee`)

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
   - Added `sync-model-data` (`src/sync/model-data.ts`) to fetch public
     WoWDBDefs table column definitions and TACTKeys encryption keys.
   - Added `scripts/copy-lib.mjs` to copy CommonJS exporter modules and licenses
     to `dist/wowexport/`.

5. **v0.8.0 (NPC Armor/Gear Geosets & Released WoW Forever Client):**
   - NPC creature export now includes armor geosets and attachment bones for
     helms, shoulders, and weapons.
   - Added support for released WoW Forever client under `_forever_` / product
     `wowf` (replacing beta `_classic_beta_` / `wow_classic_beta`), with full
     global, event, and CVar API lists.

6. **v0.8.1 (Midnight 12.1.5 Interface & CASC Journal Index Fix):**
   - Bumped Retail (`mainline`) Interface version from `120100` to `120105` for
     patch 12.1.5.
   - Updated `CASCLocal.loadIndexes` to load only the newest `.idx` journal file
     per 2-hex-digit bucket after patches.
   - Synced Retail and WoW Forever API indexes.

7. **v0.9.0 (Local Item Search & Client Hotfix Cache Reader):**
   - Added 28th tool `wow_item_search` (`src/tools/items.tools.ts`), combining
     CASC `Item`/`ItemSparse` DB2 tables with `Cache/ADB/<locale>/DBCache.bin`
     (`HotfixReader.js`, XFTH v9) so post-build and WoW Forever custom items
     are searchable.
   - Supports `newOnly` (`new`) vs. `changedOnly` (`hotfix`) filtering,
     quality/binding/level/slot filters, and optional JSON export via
     `outputFile` (`readOnlyHint: false`).
   - Added `Item` and `ItemSparse` to `DBD_TABLES` in `src/sync/model-data.ts`.

## Local Customizations

- **Antigravity Plugin (`plugins/wow`):**
  - Registered MCP server configuration in `plugins/wow/mcp_config.json`.
  - Conditional addon development and 3D asset export rules in
    `plugins/wow/rules/wow_addon_development.md` using `trigger: model_decision`
    frontmatter for progressive disclosure (preventing rule pollution in non-WoW
    projects).
  - Four on-demand skills: `wow-addon-feature`, `wow-addon-troubleshoot`,
    `wow-addon-scaffold`, and `wow-model-export`.
- **Dependency Tracking:**
  - Modernized dependency versions (`fast-xml-parser` v5, `zod` v4,
    `@types/node` v26, `typescript` v7, `tsx` v4, `express` v5).
  - Configured Dependabot and lifecycle scripts allowlisting (`allowScripts` in
    `package.json`).
  - Added `"types": ["node"]` in `tsconfig.json` for TypeScript 7 +
    `@types/node` 26 compatibility.
