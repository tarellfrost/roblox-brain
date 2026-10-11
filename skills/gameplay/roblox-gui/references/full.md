# Boilerplate UI Screens: Full Reference

## Project priority and scope

These skills target tarellfrost/roblox-game-boilerplate as supplied in the
master ZIP. Follow explicit user choices and the project's current AGENTS.md;
then use its existing source, registries, manifests, and module contracts to
resolve implementation details. This is a project convention, not a claim that
every Roblox game must use this architecture. In another project, confirm the
architecture before applying boilerplate-specific paths or APIs.

Code is authored on disk under src/server, src/client, and src/shared and synced
with Rojo. Studio owns the authored place, geometry, Lighting, and StarterGui.
Mapped modules live directly under Roblox service roots; there are no additional
Server, Client, or Shared wrapper folders in Studio. Keep --!strict, PascalCase
public APIs, camelCase locals, and the repository's StyLua settings. Code examples
below are illustrative integration examples; verify them in the target project
with scripts/check.ps1 and a feature-appropriate Studio playtest. Only directives
such as --!strict belong in generated code comments under the supplied policy.

## Screen ownership and authoring

The visual requirements below record this fork owner's choices on 2026-10-11;
they are mandatory for this skill, not universal Roblox conventions. Later
explicit user choices can override them. Preserve the boilerplate's existing
ScreenGui-per-screen loader and single UIController. Do not regroup everything
into HUD/Menus/Overlays or change the loader without a new request.

Create StarterGui.Hud, Shop, or another named ScreenGui in Studio. Inspect the
existing tree before changes, preserve its visual language, and save the place.
The code-only Rojo build does not contain these authored assets. Use responsive
layouts/constraints rather than per-frame pixel calculations. Leave device testing to the user unless requested.

Add src/client/Controllers/UIController/Screens/Hud.luau and an entry
Hud = require(script.Screens.Hud) to the existing modules table. This table is
empty in the supplied boilerplate. Keep a single UIController registry entry;
screens are created objects, not lifecycle systems.

## Layout decisions and scope

Use consistent scale-based gaps between navigation buttons. Keep enough visible
space that neighboring borders never touch, including press/hover/selection
states. Account for strokes and bounded enlargement. Do not prescribe one
reference's spacing: examples vary, and the requirement is clear separation.
Never enlarge buttons merely to fill their layout cells.

Every navigation button must have a distinct purpose and destination. Never add
Index and Collection as separate buttons leading to the same screen unless the
user explicitly defines different functionality. Do not invent destinations,
labels or features merely to fill a grid; an incomplete grid is acceptable.

Keep Settings grouped with the main navigation. Place it elsewhere only when
the user's reference or explicit instruction calls for that placement. Derive
navigation, placement and visual hierarchy from the request and references.
Do not add branding, banners, menus, promotions or gameplay features solely to
fill empty space. Reference screenshots do not authorize extra scope.

For UI-only requests, implement presentation and UI interactions: navigation,
scrolling, tabs, selection, transitions and visual feedback. Gameplay buttons
may show bounded press feedback, but must not change progression, grant rewards,
trigger purchases or introduce gameplay services, remotes or data schema changes.
State/packet integration examples below apply only when requested.

Mobile-friendly authoring means scale-only geometry, readable text, usable touch
controls, compact menus and room for Roblox touch controls. Perform a brief
structural check; do not repeatedly test devices, orientations or scaling unless
the user requests it. Leave device testing to the user and report what was checked.
Never claim device verification that was not performed.

Before delivery, ask internally: Does each button lead somewhere unique, and is
every added element justified by the request or references?

## Preserve visual quality when applying rules

- For a fresh build, use the references to establish proportions,
  typography hierarchy, texture density and composition. Apply the
  owner's rules to that design; do not substitute a generic layout
  merely because it satisfies the individual rules.

- Button separation means a modest visible gap between outer borders.
  It does not mean widely separated tiles. Adjust layout padding
  without unnecessarily changing button size or navigation proportions.

- Tune stud TileSize for each surface's dimensions and aspect ratio.
  Avoid oversized, stretched or excessively dense studs. Navigation
  tiles and wide progression bars need different tile proportions.

- Size text by its role: prominent resource totals and primary actions,
  clear progression labels, and readable navigation captions. Extra
  words must not crowd or shrink the existing hierarchy.

- A static UI request removes behavior, not visual quality. Preserve
  the same styling, artwork space and composition without scripts.

- Before delivery, inspect one overall HUD view. Check the composition
  as a whole, not merely whether each property follows a rule.
  Do not expand this into repeated device testing.

## Screen contract and example

context.Controllers exposes controller owners; context.Open and context.Close
navigate without direct screen requires. Modal screens exclude other modals;
non-modal HUD/notifications may coexist. UIController sets ResetOnSpawn=false,
tracks open state, waits for authored GUI with timeouts, and cancels pending
loads at teardown. Do not duplicate those responsibilities in each screen.

Illustrative Hud module; author a TextLabel named Cash under StarterGui.Hud:

```luau
--!strict

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Trove = require(ReplicatedStorage.Packages.Trove)
local Types = require(script.Parent.Parent.Types)

local Hud = {}

function Hud.new(context: Types.Context, gui: ScreenGui): Types.Screen
	local trove = Trove.new()
	local cashLabel = gui:FindFirstChild("Cash", true)
	if cashLabel and cashLabel:IsA("TextLabel") then
		trove:Add(context.Controllers.DataController:Observe({ "Cash" }, function(value: any)
			cashLabel.Text = if type(value) == "number" then string.format("%d", value) else "..."
		end))
	else
		warn("[Hud] Expected TextLabel at PlayerGui.Hud.Cash")
	end
	return {
		Modal = false,
		StartsOpen = true,
		Open = function(_self: Types.Screen)
			gui.Enabled = true
		end,
		Close = function(_self: Types.Screen)
			gui.Enabled = false
		end,
		Destroy = function(_self: Types.Screen)
			trove:Destroy()
		end,
	}
end

return Hud
```

## Interaction and state

Connect authored GuiButton.Activated through the screen Trove. For a request
that yields, use owned asynchronous work, a pending flag, and a destroyed or
generation check before updating the screen on completion. Define pending,
denied, accepted, and timed-out states. Do not optimistically grant currency or
inventory; DataController observations display the confirmed state.

Use a screen-lifetime Trove for subscriptions and an open-lifetime owner for
resources that should exist only while visible. Hiding a GUI does not destroy
its connections. Repeated Open/Close must not accumulate handlers or create
duplicate requests. Restore gamepad focus when a modal closes and avoid consuming
gameplay input behind the modal. Complex placement/camera behavior belongs in a
separate controller accessed through context.Controllers.

## Scale-only layout: mandatory

NEVER use offset components for UI positioning or sizing. Use
UDim2.fromScale(x, y) and UDim.new(scale, 0). Audit every descendant, helper,
responsive variant, interaction state and animation endpoint, not just root
panels. Do not use UDim2.fromOffset or mixed scale/offset UDim2 values.

| Layout property | Required treatment |
| --- | --- |
| Position and Size | Scale only; AnchorPoint expresses the intended anchoring. |
| UIPadding and layout padding | UDim scale with offset zero. |
| UIGridLayout CellSize and CellPadding | Scale-only UDim2; reflow column counts by available space. |
| UICorner CornerRadius | UDim scale with offset zero; match reference shapes. |
| ImageLabel/ImageButton TileSize | Scale-only UDim2; tune tile proportions per surface. |
| ScrollingFrame CanvasSize | Scale only, or use AutomaticCanvasSize for overflowing content. |
| Geometry tween goals | Scale-only Position/Size; bounded UIScale feedback may complement them. |

Use dimensionless aspect constraints when preserving icon/card proportions.
Do not impose fixed pixel panel minimums or compute a fixed desktop design in
pixels and convert it to scale at runtime. A single width-based UIScale applied
to an entire desktop HUD is not a responsive layout strategy.

Some engine properties, such as TextSize and UIStroke.Thickness, are scalar
numbers rather than UDim layout values. They do not authorize offset positioning
or sizing. Measuring the rendered result in pixels to assess legibility and touch
targets is allowed; layout geometry remains scale-only.

## Mobile-first layout and compact menus: mandatory

Phones are the primary audience. Plan both portrait and short landscape before
desktop. Consider available width, height, aspect ratio and safe areas together;
respond to orientation changes while a menu is open. Respect device cutouts,
Roblox top controls and the actual movement/jump control regions. Keep gameplay
visible and essential actions reachable without obstructing these regions.

Define distinct HUD regions for navigation, currencies/progress, main action and
secondary actions. Reflow or collapse secondary groups on small screens instead
of letting regions collide. Reduce grid columns or scroll content when needed;
do not shrink every element until text and buttons become unusable. Preserve
large, useful artwork areas with UIAspectRatioConstraint where appropriate.

Touch input must work without hover. Author usable scale-based tap areas and retain separation between neighboring actions; leave device testing to the user unless requested.
Keep important descriptions readable; let longer/localized text wrap or grow
rather than endlessly shrinking it with TextScaled. Use gamepad focus and
equivalent touch/selection feedback for interactions.

Menus must not take up the entire screen. Choose content-fitting scale bounds
for each orientation and leave visible surroundings. Avoid giant empty bodies,
desktop-sized minimums and oversized titles. Keep title, close control and main
action reachable; scroll overflow inside a ScrollingFrame. A full-screen modal
dismissal/dimming scrim is separate from the compact menu panel. No universal
menu percentage replaces comparison with the selected screenshot and device.

## Typography, stud surfaces and gradients: mandatory

Use Montserrat only. Titles use Black weight (900), represented by
Enum.FontWeight.Heavy in Roblox; descriptions use Enum.FontWeight.ExtraBold
(800). Set FontFace explicitly; never use Fredoka One. Do not invent a
FontWeight.Black enum. Preserve the reference's hierarchy and strong black text
outlines while keeping counters and small captions legible.

Illustrative styling for existing authored labels:

```luau
--!strict
local function applyTypography(title: TextLabel, description: TextLabel)
	local family = "rbxasset://fonts/families/Montserrat.json"
	title.FontFace = Font.new(family, Enum.FontWeight.Heavy, Enum.FontStyle.Normal)
	description.FontFace = Font.new(family, Enum.FontWeight.ExtraBold, Enum.FontStyle.Normal)
	title.TextScaled = true
	description.TextScaled = true
end
return applyTypography
```

Use ImageButtons instead of TextButtons for studded controls. Use ImageLabels for
studded headers and backgrounds. Put captions in child TextLabels and connect
GuiButton.Activated; do not assume a button has TextButton.Text. Set the surface
Image to rbxassetid://6927295847, ScaleType to Enum.ScaleType.Tile and
ImageTransparency to 0.5. Apply scale-only TileSize. Never build studs from
individual Frames. Set surface UIStroke.ApplyStrokeMode to
Enum.ApplyStrokeMode.Border so it outlines the surface. Text outlines are separate.

Illustrative styling for an existing ImageButton and its border; tile values
demonstrate syntax and must be tuned against the actual surface proportions:

```luau
--!strict
local function applyStudSurface(surface: ImageButton, border: UIStroke)
	surface.Image = "rbxassetid://6927295847"
	surface.ScaleType = Enum.ScaleType.Tile
	surface.ImageTransparency = 0.5
	surface.TileSize = UDim2.fromScale(0.12, 0.24)
	border.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
end
return applyStudSurface
```

Check that the texture loads in the target place. If unavailable, report the
asset problem rather than silently replacing it with frame studs. The asset ID
is the owner's choice; it is not evidence of licensing or universal access.

USE GRADIENTS EVERYWHERE except white text: visible headers, cards, backgrounds,
buttons, progress fills and colored text. Apply UIGradient to the component that
actually renders the color, not merely a transparent structural container.
Keep white text white, maintain contrast and choose gradient colors/direction
from the reference. Plain structural containers need no decorative surface.
For all-black UI Frames, set BackgroundTransparency to 0.5; do not create opaque
black menu bodies. Fully transparent layout containers remain transparent.

## Image slots and visual matching: mandatory

Before implementation, open the chosen screenshot and record its viewport-relative
panel proportions, card/icon proportions, typography hierarchy, outlines, texture
density, gradient direction, spacing and visual density. Match these properties;
bright colors and a similar arrangement alone do not establish a style match.

Use large ImageLabel artwork slots distinct from the tiled background. Preserve
artwork proportions, normally with ScaleType.Fit. If artwork is not supplied,
leave Image empty and use descriptive instance names for later assignment.
Do not draw small boxed ART placeholders or replace missing artwork with a tiny
caption. Allow intentional icon overlap when present in the reference, while
keeping controls accessible and avoiding accidental clipping.

Match strong black surface borders and corners to the reference rather than
applying generic pill shapes or white icon boxes. Do not add unrequested branding,
tutorial banners, promotions, purchase pressure or economy mechanics as decoration.
Screenshots guide visual appearance, not permission to copy every game mechanic.

## Juice after layout passes

Add responsive feedback after scale-only layout and reference structure are established.
Use bounded press/selection/hover feedback, short menu transitions, smooth progress
updates, count-ups and milestone celebrations. Touch must receive equivalent
feedback without hover. Reward effects reflect server-confirmed state, never a
client-granted outcome. Tween geometry uses scale-only values.

Keep effects local to their owning region, readable and brief; do not cover
controls, create overlapping HUD regions or loop attention effects constantly.
Support reduced effects without losing information. Own tweens, subscriptions,
sounds and temporary objects through the screen's cleanup lifecycle. Reopening
menus must not multiply animations, handlers or sound playback.

## Accessibility and missing assets

Use ScreenGui for overlays, SurfaceGui for surfaces, and BillboardGui for world
labels. Repeated layout belongs to UIListLayout/UIGridLayout and constraints.
Allow localized text to grow; check scrolling, clipping, safe areas, contrast,
touch targets, and navigation focus before adding visual polish. Do not hardcode
one device as the sole acceptance target.

If Studio MCP is unavailable, deliver the module and explicit authored-instance
requirements; report the remaining GUI work. Do not silently change to React,
Fusion, or Instance.new UI. If the user asks for generated/reactive UI, explain
how its rendering owner fits the existing screen contract and dependencies.

## Visual style references

When the user asks for a reference style, locate INDEX.md inside the root
ui-references folder of the available full roblox-brain checkout. This library may be absent in
a skills-only installation. A user-supplied screenshot can also serve directly
as a reference without the library. Do not assume a relative filesystem path
from an installed skill points back to the original repository.

Select by visual tags and screen purpose, read the chosen STYLE.md, and open
its relevant images using an image-viewing tool. Establish the primary style
before mixing references. Extract proportions, colors, typography, shapes,
outlines, spacing, and density; apply them through the existing screen contract
and Studio authoring workflow. A still image is not evidence of animation or
interaction behavior. Confirm reuse rights before copying referenced artwork.

The initial colorful-rounded style is explicitly a template with no screenshots.
Its draft preferences are usable as a written brief, but cannot establish visual
similarity to an unseen game. Report unavailable images or missing library access
and continue only with the references/brief actually available. Compare a representative result image with the reference before claiming visual similarity; do not expand this into a device capture matrix unless requested.

## Verification

Keep checks proportional to the request. Mobile-friendly authoring does not
imply a repeated device/orientation test pass.

1. Briefly audit authored instances and changed code for scale-only geometry,
   Montserrat weights, tiled asset/transparency, border strokes, gradients,
   black-frame transparency and absence of frame-built studs or ART boxes.
2. Check that each navigation button has a distinct destination, Settings is
   grouped as requested, and every added element is justified by the brief or
   references. For UI-only work, confirm no gameplay services, progression,
   reward grants or purchases were introduced.
3. Run relevant code checks and a brief UI interaction check when practical.
   If claiming visual similarity, compare a representative result with the
   reference for proportions, typography, texture, borders and density.
   Report limitations; code checks alone do not establish visual similarity.
4. Leave device-by-device and orientation testing to the user unless requested.
   If requested, scope checks to affected screens and target devices; expand
   only when new failures or changes warrant it. Avoid repeated full matrices.
5. Save the authored place when possible, or state that the user must save it.
   Report only checks actually performed. Untested device behavior does not
   block delivery of scale-based UI when the user owns device testing.

Use `roblox-input`, `roblox-localization` and `roblox-networking` as needed and
within the requested scope.
## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.

The 2026-10-11 UI revision records the owner's scale-only, mobile-first,
Montserrat, stud-texture, gradient, transparency and visual acceptance rules.
The brainrot library contains 24 captures (22 unique); consult its manifest and
reference catalog when selecting images. These owner rules supersede conflicting
draft pixel-layout or typography suggestions in older style notes.

Image surface and stroke documentation was consulted for this revision;
Montserrat's 800/900 weight mapping was checked against the installed Studio
font-family data and available engine types. The provided texture's reuse rights
were not verified. Editing this skill does not constitute testing an experience
or producing device screenshots.
