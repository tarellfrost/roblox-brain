# Colorful Rounded

**ID:** colorful-rounded

**Status:** Template only — awaiting screenshots

**Tags:** colorful, rounded, chunky, outlined, playful

**Potential uses:** simulator HUDs, tycoon upgrades, inventories, collections

## Visual direction

These are starter preferences, not observations from an existing game. Replace
or refine them after adding the references you want the agent to reproduce.

| Property | Starter direction |
| --- | --- |
| Palette | Bright accents on calmer panel backgrounds; one clear primary-action color |
| Shapes | Rounded panels and large, chunky buttons with consistent corner treatment |
| Outlines | Consistent visible outlines that separate controls from the game world |
| Typography | Readable, bold headings; simple body labels; consistent number formatting |
| Spacing | Generous touch targets and clear gaps between distinct interaction groups |
| Hierarchy | Screen title, main content, primary action, then secondary controls |
| Density | Focused HUD; repeated items use a grid; details appear when selected |
| States | Clear normal, focused, pressed, disabled, pending, and confirmed states |

Use one consistent treatment across HUD, shop, and inventory. Do not turn every
element into the primary action through equally saturated colors and motion.

## Screenshot slots

Add files to `screenshots/`. These names describe planned uploads; the images
are not included yet. Supply the screens relevant to your style rather than
forcing every category to exist.

| Planned file | What it should show | Properties to match |
| --- | --- | --- |
| hud-desktop.png | Full gameplay view with HUD | Placement, scale, world readability, hierarchy |
| shop-desktop.png | Shop or upgrade panel | Panel shape, offer cards, typography, primary action |
| inventory-desktop.png | Inventory or collection screen | Grid, selected-item state, rarity treatment, spacing |
| mobile.png | One reference at a phone viewport | Responsive layout, safe areas, touch targets |
| button-detail.png | Close crop of a representative button | Fill, outline, corners, icon/text relationship |

## Source records

Fill one row per uploaded image. Record inspiration-only versus reusable assets;
do not assume possessing a screenshot grants permission to reuse its artwork.

| File | Source URL or owned project | Creator | Permission / reuse status | Viewport / device | What to reproduce |
| --- | --- | --- | --- | --- | --- |
| First uploaded screenshot | Fill when uploaded | Fill when uploaded | Inspiration-only / licensed / owned | Fill when uploaded | Fill when uploaded |

## Matching notes

- **Match:** the selected references' component shapes, palette relationships,
  type hierarchy, outline consistency, spacing, and visual density.
- **Adapt:** labels, game-specific content, branding, and icons unless their
  reuse is authorized. Preserve the target game's functional requirements.
- **Do not infer from still images:** animation timing, sounds, hidden states,
  responsiveness, or gameplay success. Add a clip or notes if those matter.
- **Priority:** user-selected references and explicit game requirements precede
  this draft style description; the current game AGENTS.md governs architecture.

## Feedback direction

Ordinary presses need quick confirmation; successful upgrades should show the
confirmed state and an appropriate celebration. Bigger milestones may receive
stronger feedback without blocking the next useful action. Keep a practical
reduced-effects option. These are proposed behavior preferences until a clip or
explicit instruction supplies actual timing and motion references.

## Roblox implementation

Author ScreenGuis in StarterGui and save the place. Implement screens under
`src/client/Controllers/UIController/Screens`, register them in UIController's
modules table, and use DataController for confirmed state and Packet for intent.
Do not create an alternative controller, framework, or raw-remote hierarchy
to reproduce a screenshot's appearance. Use existing project styling/components
where they already serve the requested visual direction.

## Review the result

- Compare actual result screenshots with the selected references, not memory.
- Check proportions, palette, typography, shapes, outlines, spacing, hierarchy,
  density, and authored assets at relevant desktop/mobile sizes.
- Test focus, pressed/disabled/pending states, long labels, and readability over
  the world. Missing states require an explicit design choice, not invented evidence.
- Record mismatches and iterate. Describe a result as visually verified only
  when the implementation and relevant images were actually inspected.
