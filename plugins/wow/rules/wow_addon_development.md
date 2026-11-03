---
trigger: model_decision
description:
  Use this rule when developing, maintaining, or modifying World of Warcraft
  addons, FrameXML UI, or WoW Lua code, or when searching and exporting game
  models, creatures, textures, and terrain.
---

# World of Warcraft Addon Development & Asset Extraction Rules

When developing, maintaining, or modifying World of Warcraft addons, or when
extracting 3D assets from game files, you must adhere to the following
principles:

## 1. Zero-Guesswork API & CVar Grounding

- **Never rely on training memory for WoW APIs or console variables.** Blizzard
  frequently refactors APIs between major expansions (notably the 10.0
  Dragonflight, 11.0 The War Within, and 12.0 Midnight overhauls).
- Always use `wow_api_search` to verify:
  - Exact namespace (e.g., `C_Item.*`, `C_Spell.*`, `C_Container.*`,
    `C_UnitAuras.*`).
  - Expected arguments, nilability, and parameter types.
  - Return signatures (many legacy multi-returns are now structured tables).
  - Taint permission level and Midnight secret restrictions (`secretArguments`
    and `secretReturns`).
- Verify console variables with `wow_cvar_search` before using `GetCVar` or
  `SetCVar`. Confirm default values, account vs. character scope, Blizzard UI
  usage examples, and the `secure` flag (protected CVars fail in combat).
- When an API is missing or differs between clients, check `wow_api_diff` to
  determine availability across Retail (`mainline`), Classic progression
  (`mists`), Classic Era (`vanilla`), and WoW Forever (`forever`).

## 2. Blizzard-First UI Discovery

- Do not build complex widgets, buttons, or scroll views from scratch in Lua.
- Use `wow_ui_template_search` and `wow_ui_mixin_search` to find and inherit
  existing Blizzard templates (e.g., `UIPanelButtonTemplate`,
  `BasicMessageDialogTemplate`, `ScrollBoxList`).
- Inspect Blizzard's reference implementations using `wow_ui_grep`,
  `wow_ui_read_file`, and `wow_ui_list_packages` to observe how stock FrameXML
  handles similar patterns.
- Pass the appropriate `flavor` parameter to all UI source tools. Unsynced
  flavors return clear sync instructions rather than falling back to Retail.

## 3. Execution Taint, Secret Values & Combat Lockdown

- **Protected Functions:** Never call protected functions (`CastSpellByName`,
  `TargetUnit`, `AttackTarget`, etc.) from generic addon execution paths. Use
  `SecureActionButtonTemplate` attributes or secure handlers.
- **Combat Lockdown:** Do not show, hide, re-anchor, or resize secure frames
  while `InCombatLockdown()` is true. Defer updates until
  `PLAYER_REGEN_ENABLED`.
- **Midnight Secret Values:** When an API returns `secretReturns: true`, addon
  code cannot inspect, format, or branch on the returned values. Respect
  `secretArguments` flags (`AllowedWhenUntainted`, `AllowedWhenTainted`,
  `NotAllowed`).
- **Safe Hooking:** Never overwrite Blizzard global functions or tables. Use
  `hooksecurefunc(functionName, hook)` or
  `hooksecurefunc(table, methodName, hook)` for post-hooks.
- **Global Pollution:** Namespace all globals under the addon's unique prefix,
  or use the private table `local addonName, ns = ...` passed into each addon
  file.

## 4. Texture and Asset Resolution

- Never invent hardcoded file paths (e.g.,
  `"Interface\\AddOns\\MyAddon\\texture.png"`).
- **Prefer UI Atlases for UI Chrome:** Search `wow_atlas_search` with the target
  `flavor` first. When an official Blizzard atlas exists (e.g.,
  `common-icon-plus`), prefer `frame:SetAtlas(atlasName)` over numeric
  `FileDataID`s.
- **Reverse-Verify Numeric FileDataIDs:** When using `wow_icon_search` or
  numeric IDs, always run a reverse verification check via `wow_file_search` on
  the numeric ID to confirm it resolves back to the expected texture name.
- **Mandatory Inline Comments:** Whenever placing a raw numeric `FileDataID` in
  Lua or `.toc` files, always include an inline comment with the canonical
  texture name (e.g.,
  `b.Icon:SetTexture(135769) -- FileDataID: spell_chargepositive`).
- **Texture Previews:** When an asset's visual appearance needs inspection,
  decode the `.blp` texture to `.png` using `wow_texture_export`.

## 5. Headless 3D Model, Creature & Terrain Export

- **Verify Prerequisites:** Ensure local game storage and data sets are ready.
  Check `wow_install_info` and `wow_data_status`. Model, creature, texture, and
  terrain export tools require both `sync game-data` and `sync model-data`.
- **Search Before Exporting:** Always resolve models via `wow_model_search`,
  creatures or NPCs via `wow_creature_search`, and map IDs/tiles via
  `wow_map_search` before invoking export tools.
- **Confined Output Directories:** Always pass an explicit, dedicated
  `outputDir`. Exports preserve the game's internal directory structures so
  textures resolve relative to models in Blender.
- **Reference Specialized Skill:** Activate the `wow-model-export` skill for
  detailed 3D export workflows (glTF animations, NPC armor geosets, attached
  helm/shoulder/weapon models with rest-pose bone coordinates, and ADT terrain
  tile exports).

## 6. Mandatory Validation Gate

Before completing any task that adds, edits, or refactors addon files:

- Run `wow_lua_lint` on all touched `.lua` files (or inline snippets) for all
  supported client flavors. Resolve all errors and warnings. Pass `knownGlobals`
  when using external libraries.
- Run `wow_toc_validate` on all touched `.toc` manifests or addon directories to
  verify Interface numbers, directives, and file lists.
- Run `wow_xml_validate` on all touched `.xml` files (supporting both `<Ui>`
  `UI.xsd` and `<Bindings>` files).
