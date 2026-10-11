# Luau Patterns for the Boilerplate: Full Reference

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

## Choose a module shape

A lifecycle system is a singleton service or controller with Name and optional
Init, Start, Destroy. A pure child module transforms values or validates input
without connecting events or holding module-level feature state. A metatable
object is appropriate for several independent owned values, such as projectiles
or effect leases. Tagged world objects generally use the Component package.

Constructors use a dot; object methods use a colon. Keep mutable object fields
on the instance. Export shared types from Types.luau. Prefer concrete context
types at helper boundaries; registry dictionaries in the supplied framework are
dynamic, so validate required entries and narrow data at the edges.

## Dependencies and module loading

Require Roblox packages from ReplicatedStorage.Packages or server packages from
ServerScriptService.ServerPackages. Require child and pure sibling modules
directly. Systems capture the complete registry in Init; they do not require
other services or controllers directly. Child modules receive dependencies as
arguments, and never require their owner init.luau.

Feature requires only define tables, types, and functions. Existing Packet and
data-signal definitions are framework exceptions already present in the supplied
source; they do not justify creating connections or starting loops in new modules.

## Cleanup and asynchronous work

Use Trove to own ordinary connections, generated instances, and package objects
with compatible cleanup methods. Add cleanup functions for operations such as
UnbindAction, UnbindFromRenderStep, state restoration, or task cancellation.
Validate a package's installed cleanup contract instead of assuming Add accepts
every resource shape. Destroy must work if Init only partially completed.

Illustrative controller structure with an owned render connection:

```luau
--!strict

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Trove = require(ReplicatedStorage.Packages.Trove)

local EffectController = { Name = "EffectController" }
local trove: any = nil

function EffectController.Init(_self: any, _registry: { [string]: any })
	trove = Trove.new()
end

function EffectController.Start(_self: any)
	trove:Add(RunService.PreRender:Connect(function(_dt: number)
		local camera = workspace.CurrentCamera
		if not camera then
			return
		end
	end))
end

function EffectController.Destroy(_self: any)
	if trove then
		trove:Destroy()
		trove = nil
	end
end

return EffectController
```

This is a wiring specimen, not an effect implementation. Register a real owner
before using it. Prefer an existing controller when it owns the same concern.

For asynchronous loads, use a destroyed flag or generation token so old results
cannot modify a replacement character, closed screen, or stopped component.
Use bounded waits, observable failures, and task handles when cancellation is
needed. Never task.spawn every Init/Start; it loses registry startup guarantees.
An asynchronous task must report its own failure and decide whether to disable
the feature, retry safely, or request a restart.

## Data-release exception

DataService is registered first and destroyed last. Its PlayerReleasing fires
during its Destroy, after other services have run Destroy. A feature's final-save
listener must remain connected until release completes and must not access
already-destroyed resources. Keep those listeners separate from an ordinary Trove;
callbacks read final state and write synchronously without yielding. The supplied
DataService does not destroy these Signal objects, so treat this as a session
shutdown convention, not a reusable hot-reload cleanup strategy. A restartable
service would need coordinated teardown. See `roblox-data`.

## Signals and failure semantics

Use Signal for independent observers; direct registry calls for operations with
one known owner. Connect in Start, type payloads consistently with the installed
package, and keep event consumers non-yielding when ordering matters.

pcall returning true with nil is successful missing data, not an exception.
DataService:GetData returning nil means no usable profile. RequestSave returning
true means a save was requested, not durably acknowledged. A Packet timeout is
an unknown outcome, not proof that the server did nothing. Retries of mutations
need an idempotency contract rather than a blind second call.

## Review

Check cleanup by following resource ownership through normal removal, partial
startup, respawn, streaming, and shutdown. Searches for Trove or Destroy alone
do not prove cleanup. Keep pure logic testable without inventing a second framework.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
