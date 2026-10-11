---
name: roblox-gui
description: "Use for Studio-authored Roblox UI, UIController screen modules, reactive DataController views, and cross-platform layout."
last_reviewed: 2026-10-02
sources:
  - https://create.roblox.com/docs/ui
  - https://create.roblox.com/docs/ui/position-and-size
  - https://create.roblox.com/docs/ui/styling
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate UI Screens

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Author ScreenGuis and layout in StarterGui in Studio; save the place. Rojo does not serialize this UI under the supplied mapping.
- UIController owns all screens. Add Screens/<Name>.luau and register it in UIController/init.luau's modules table; do not add one controller per screen.
- Screens return new(context, gui) and an object with Modal, StartsOpen, Open, Close, Destroy. Use the existing Types contract.
- Use context.Open/Close to navigate and context.Controllers to access systems; screens never require one another.
- Read player state through DataController:Observe and clean connections with Trove. Send intent through Packet; the server decides outcomes.
- Find authored elements by name and check class before using them. Missing GUI disables only that screen through the existing loader.
- Prefer layouts, constraints, readable text, safe areas, touch targets, and gamepad focus. Verify open/close and respawn behavior.
- If Studio tooling is unavailable, finish screen-module code and report the missing authoring step. Generate UI in code only when the user chooses that approach.

> Implementation contracts and examples: [references/full.md](references/full.md).
