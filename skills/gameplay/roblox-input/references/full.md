# Boilerplate Input Ownership: Full Reference

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

## Owner and package choice

Use a controller with its own state/lifetime for camera, placement, vehicle, or
combat input. Avoid a generic input manager that merely forwards unrelated
feature requests. A screen's GuiButton.Activated handler belongs to that screen;
it calls a controller operation through context.Controllers if needed.

Input is already a Wally dependency. The supplied guide documents
Input.Keyboard.new(), Mouse.new(), Gamepad.new(), Touch.new(), and
Input.PreferredInput.observe(callback). Inspect the installed package source and
types before using events or additional methods; do not invent a shared Bind
API or assume callbacks have UserInputService's signatures.

Create only the devices the feature uses, during Start. Add objects and
subscriptions to Trove when their cleanup contracts match; otherwise add explicit
cleanup functions. Do not add another input package for a single action.

## ContextActionService integration

ContextActionService complements Input when engine action/context priority is
needed. Illustrative controller toggling an authored Shop screen; register it
after UIController and add a matching Shop module/ScreenGui before using it:

```luau
--!strict

local ContextActionService = game:GetService("ContextActionService")

local MenuController = { Name = "MenuController" }
local controllers: { [string]: any } = {}
local actionName = "ToggleShop"

function MenuController.Init(_self: any, registry: { [string]: any })
	controllers = registry
end

function MenuController.Start(_self: any)
	ContextActionService:BindAction(actionName, function(_name, state, _input)
		if state == Enum.UserInputState.Begin then
			local ui = controllers.UIController
			if ui:IsOpen("Shop") then
				ui:Close("Shop")
			else
				ui:Open("Shop")
			end
		end
		return Enum.ContextActionResult.Sink
	end, false, Enum.KeyCode.M, Enum.KeyCode.ButtonY)
end

function MenuController.Destroy(_self: any)
	ContextActionService:UnbindAction(actionName)
end

return MenuController
```

The false argument avoids generating a second mobile UI path: author the touch
button in Studio and let the Shop/HUD screen call the same operation. Production
actions must decide when to Sink versus Pass and how chat, text fields, modal
menus, and existing engine controls affect the binding. CAS handlers receive
action name, state, input, not gameProcessedEvent.

## Device and context lifetimes

Use PreferredInput observation to adapt prompts/touch controls on mixed-device
hardware. Confirm its installed callback values instead of assuming an enum.
Gamepad focus and gameplay bindings are separate concerns; set selection on
menu entry and restore it on exit. When using raw UserInputService for a device
capability not covered by Input, honor processed input and own each connection.

Clear held keys, tracked fingers, drag ownership, and temporary mouse locks when
focus/context/character changes. A second finger must not hijack an existing
drag. Restore prior mouse behavior and UI state when the owner exits. Throttle
continuous network actions deliberately; never send a packet each rendered frame
merely because input is sampled then.

## Authority and verification

Packet requests describe intent, not the result. Services validate permissions,
rate, state, and targets before acting. A client placement preview does not
authorize the placed object; a vehicle throttle does not prove valid speed.
If a project explicitly uses engine Server Authority, verify current InputAction
and synchronized-simulation APIs/settings before integration. Do not silently
enable that model as part of an input feature.

Test keyboard, touch, gamepad, mid-session device change, text entry, competing
bindings, focus loss, respawn, controller destruction, and malformed requests.
Use `roblox-gui` and `roblox-networking` for the adjacent contracts.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
