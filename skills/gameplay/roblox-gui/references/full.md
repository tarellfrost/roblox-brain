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

Create StarterGui.Hud, Shop, or another named ScreenGui in Studio. Inspect the
existing tree before changes, preserve its visual language, and save the place.
The code-only Rojo build does not contain these authored assets. Use responsive
layouts/constraints rather than per-frame pixel calculations, and test over the
actual game scene at phone, tablet, desktop, and gamepad sizes.

Add src/client/Controllers/UIController/Screens/Hud.luau and an entry
Hud = require(script.Screens.Hud) to the existing modules table. This table is
empty in the supplied boilerplate. Keep a single UIController registry entry;
screens are created objects, not lifecycle systems.

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

## Layout, accessibility, and missing assets

Use ScreenGui for overlays, SurfaceGui for surfaces, and BillboardGui for world
labels. Repeated layout belongs to UIListLayout/UIGridLayout and constraints.
Allow localized text to grow; check scrolling, clipping, safe areas, contrast,
touch targets, and navigation focus before adding visual polish. Do not hardcode
one device as the sole acceptance target.

If Studio MCP is unavailable, deliver the module and explicit authored-instance
requirements; report the remaining GUI work. Do not silently change to React,
Fusion, or Instance.new UI. If the user asks for generated/reactive UI, explain
how its rendering owner fits the existing screen contract and dependencies.

## Verification

Test missing/wrong-class elements, delayed data, missing ScreenGui, modal
exclusion, respawn, repeated open/close, touch, gamepad, text expansion, and
teardown while a request is pending. Capture Studio screenshots when available.
Use `roblox-input`, `roblox-localization`, and `roblox-networking` as needed.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
