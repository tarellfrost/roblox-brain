---
name: roblox-audio
description: "Use for Roblox audio implemented through the boilerplate services, controllers, components, and Studio assets."
last_reviewed: 2026-07-26
sources:
  - https://create.roblox.com/docs/reference/engine/classes/SoundService
  - https://create.roblox.com/docs/audio/objects
  - https://create.roblox.com/docs/audio/effects
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Audio

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Client AudioController or emitter Component; Packet carries server-approved shared cues.
- Follow src/server, src/client, and src/shared mappings; gameplay code is ModuleScripts loaded by the existing Bootstraps.
- Register a new system explicitly in Registry.luau; register Component factories in the existing loader. Do not add standalone feature Scripts.
- Init captures dependencies and state; Start connects/schedules; Destroy or component Stop releases resources. Pure children have no lifecycle hooks.
- Use existing Trove, Signal, Input, Packet, and DataService APIs when the feature crosses those boundaries.
- Keep static assets in Studio and save the place. Runtime objects have a cleanup owner; client presentation never decides grants or damage.
- Verify the actual engine/package API before implementing unfamiliar methods. Check disk code and Studio runtime together.

> Implementation contracts and examples: [references/full.md](references/full.md).
