---
name: roblox-input
description: "Use for Roblox controller-owned keyboard, mouse, gamepad, touch, and Input-package integration with cross-platform actions."
last_reviewed: 2026-10-02
sources:
  - https://devforum.roblox.com/t/the-correct-way-to-design-mobile-buttons/2494558
  - https://create.roblox.com/docs/reference/engine/classes/UserInputService
  - https://create.roblox.com/docs/projects/server-authority
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Input Ownership

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Keep gameplay input client-side in the owning controller. UI interactions belong to UIController screens; the server validates outcomes.
- Prefer the installed Input package for device abstractions: Keyboard, Mouse, Gamepad, Touch, and PreferredInput.observe.
- Create device objects and subscriptions in Start; destroy them in Destroy. Confirm the installed version's methods before writing event bindings.
- Use ContextActionService where action priority/context handling is needed; bind in Start or context entry and unbind on exit and Destroy.
- Route intent through Packet. Do not send client-computed rewards, prices, damage, or authoritative state.
- Screen actions call controllers through context.Controllers; controllers use their captured registry.
- Prevent gameplay input while a relevant modal/text-entry context owns it. Clear held/touch state on focus loss, context exit, or respawn.
- Author mobile controls in Studio and adapt them to preferred input rather than TouchEnabled alone.
- Engine synchronized simulation input is a separate, explicitly selected authority-mode integration; the template does not enable it automatically.

> Implementation contracts and examples: [references/full.md](references/full.md).
