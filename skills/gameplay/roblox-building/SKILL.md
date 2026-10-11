---
name: roblox-building
description: "Use for Roblox building and world assets implemented through the boilerplate services, controllers, components, and Studio assets."
last_reviewed: 2026-08-31
sources:
  - https://devforum.roblox.com/t/large-scale-roblox-terrain-the-ultimate-guide/405672
  - https://devforum.roblox.com/t/a-complete-guide-to-editableimages/3858566
  - https://create.roblox.com/docs/reference/engine/classes/EditableImage
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Building and World Assets

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Studio owns static assets; services own runtime generation; Components own tagged behavior.
- Follow src/server, src/client, and src/shared mappings; gameplay code is ModuleScripts loaded by the existing Bootstraps.
- Register a new system explicitly in Registry.luau; register Component factories in the existing loader. Do not add standalone feature Scripts.
- Init captures dependencies and state; Start connects/schedules; Destroy or component Stop releases resources. Pure children have no lifecycle hooks.
- Use existing Trove, Signal, Input, Packet, and DataService APIs when the feature crosses those boundaries.
- Keep static assets in Studio and save the place. Runtime objects have a cleanup owner; client presentation never decides grants or damage.
- Verify the actual engine/package API before implementing unfamiliar methods. Check disk code and Studio runtime together.
- If advertised by the connected Studio MCP, use generate_procedural_model, generate_mesh, or generate_material; wait for completion and read back the result.

> Implementation contracts and examples: [references/full.md](references/full.md).
