---
name: roblox-gui
description: "Use for mobile-first Roblox UI, Studio authoring, UIController screens, reactive state, and screenshot-based visual matching."
last_reviewed: 2026-10-11
sources:
  - https://create.roblox.com/docs/ui
  - https://create.roblox.com/docs/ui/position-and-size
  - https://create.roblox.com/docs/ui/styling
  - https://create.roblox.com/docs/reference/engine/classes/ImageLabel
  - https://create.roblox.com/docs/reference/engine/classes/ImageButton
  - https://create.roblox.com/docs/reference/engine/classes/UIStroke
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate UI Screens

## When to Load

Load for boilerplate UI. User choices and AGENTS.md take precedence. Read the reference.

## Quick Reference

- NEVER use layout offsets: Position/Size, padding, spacing, cells, corners, tiles and tween endpoints use scale with zero offsets, including descendants.
- Mobile first: plan portrait/landscape with safe areas, readable text, usable controls and room for movement/jump controls. Menus stay compact with internal scrolling.
- Montserrat only: Black/900 titles (FontWeight.Heavy), ExtraBold/800 descriptions via FontFace. Never Fredoka One.
- Stud controls: ImageButtons; headers/backgrounds: ImageLabels. Image="rbxassetid://6927295847", ScaleType=Tile, ImageTransparency=0.5, UIStroke.ApplyStrokeMode=Border. No frame-built studs.
- Gradients everywhere except white text. All-black frames: BackgroundTransparency=0.5. Large empty artwork ImageLabels separate from tiles; no ART boxes.
- Rules constrain design, not replace it. Preserve reference proportions, text hierarchy and texture density; static UI still needs visual quality. See full.md's visual-quality guidance.
- Author StarterGui and save the place. Preserve ScreenGui per screen and one UIController; register screen modules. Runtime generation requires user choice.
- Follow Types screen contract, context.Open/Close and Trove cleanup; check names/classes. Add bounded juice with reduced effects.

## Layout decisions and scope

- Use scale-based gaps so neighboring button borders never touch.

- Each navigation button needs a unique destination. Index and Collection are one destination unless explicitly distinguished. Never add buttons to fill a grid.
- Group Settings with main navigation unless references or instructions place it elsewhere.
- Justify elements through the request or references. No filler branding, banners, menus, promotions or features; incomplete grids are acceptable.
- UI-only means presentation and UI interactions: no progression changes, reward grants, purchases or gameplay services.
- Briefly check structure; leave device/orientation testing to the user unless requested. Mobile-friendly does not imply repeated scaling tests.
- Inspect one overall HUD view; check unique destinations, justified elements and composition.

> Full guidance: [references/full.md](references/full.md).
