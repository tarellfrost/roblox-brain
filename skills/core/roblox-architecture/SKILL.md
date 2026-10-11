---
name: roblox-architecture
description: "Use for Roblox boilerplate services, controllers, registries, lifecycle, module boundaries, and client-server ownership."
last_reviewed: 2026-10-02
sources:
  - https://create.roblox.com/docs/projects/data-model
  - https://create.roblox.com/docs/projects/client-server
  - https://create.roblox.com/docs/scripting/locations
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Roblox Boilerplate Architecture

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Keep the existing two Bootstraps and shared Lifecycle. Gameplay lives in ModuleScripts, not new standalone Scripts.
- Server systems live in src/server/Services; non-UI client systems in src/client/Controllers. Register each explicitly in that side's Registry.luau.
- All Init hooks run in order before any Start hook. Init stores the registry and local state; Start binds events and starts work. Destroy handles partial startup.
- Access other systems through the captured registry, not direct system requires. Require pure helpers and child modules directly.
- Use folder/init.luau for a system with several concerns. Only init has lifecycle hooks and a registry entry; children receive dependencies as arguments.
- Tagged-instance behavior uses the installed Component package and the existing component loaders.
- Packet carries actions; Replica carries state; DataService owns profiles; PurchaseService owns receipts; UIController owns screens.
- Never put secrets or authoritative mutable state in src/shared. Use frozen Config tables for public tuning.
- Keep modules inert at require time except existing framework definitions; do not copy framework side effects into new feature modules.
- Preserve the special DataService release-listener lifetime; see roblox-data.

> Implementation contracts and examples: [references/full.md](references/full.md).
