# Roblox Boilerplate Architecture: Full Reference

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

## Runtime layout and ownership

| Disk | Studio | Role |
| --- | --- | --- |
| src/server | ServerScriptService | Authoritative rules, services, data, server components |
| src/client | StarterPlayer.StarterPlayerScripts | Controllers and client components |
| src/shared | ReplicatedStorage | Lifecycle, Packet, Packets, Config, reusable Utils |
| Packages | ReplicatedStorage.Packages | Generated shared Wally dependencies |
| ServerPackages | ServerScriptService.ServerPackages | Generated server dependencies |
| vendor/Replica | Fixed ReplicaClient/ReplicaShared/ReplicaServer paths | Vendored replication implementation |

Prefer an existing owner when extending a feature. Make a server service for
independent authoritative state, lifetime, and client requests. Make a non-UI
controller for independent client state or per-frame behavior. UI screens are
children of UIController. Pure calculations need plain modules, not lifecycle
hooks. Repeated behavior on tagged instances belongs in components.

## Registries and lifecycle

The Bootstraps require explicit registry arrays and run Lifecycle once. Registry
entries are ordered after the systems they depend on. Every Init executes before
the first Start. The complete name-to-system registry is available in Init, but
another system may not yet be initialized: store references without calling it.
In Start, initialization has completed for all systems; a later system's Start
may still be pending, so operations requiring its active subscriptions must
respect ordering or a readiness signal.

Hooks return promptly. Start schedules independent work rather than yielding on
assets, profile loads, or permanent loops. Track spawned work and handle its
failures; Lifecycle's startup error handling cannot catch later task failures.
Destroy runs in reverse initialization order and must tolerate partial Init,
missing resources, and a Start that never ran. Duplicate names and repeated
Lifecycle starts fail. There is no hot reload contract.

Illustrative non-UI controller at src/client/Controllers/CameraController.luau:

```luau
--!strict

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Trove = require(ReplicatedStorage.Packages.Trove)

local CameraController = { Name = "CameraController" }
local controllers: { [string]: any } = {}
local trove: any = nil

function CameraController.Init(_self: any, registry: { [string]: any })
	controllers = registry
	trove = Trove.new()
end

function CameraController.Start(_self: any)
	assert(controllers.UIController, "UIController must be registered")
end

function CameraController.Destroy(_self: any)
	if trove then
		trove:Destroy()
		trove = nil
	end
end

return CameraController
```

Add its require to src/client/Registry.luau. This skeleton illustrates wiring;
do not add it unless the game needs a custom camera owner.

## Feature decomposition

Use Services/ShopService/init.luau, Catalog.luau, Purchasing.luau, and Types.luau
when shop concerns become distinct. Only init is registered and owns packet
handlers and lifecycle state. Child functions receive the registry or a typed
context; they never require init or another system directly. Roughly 300 lines
is a review cue, not a reason to split coherent code arbitrarily.

Use registry calls for directed system dependencies. Use the installed Signal
package for independent subscribers when a cycle would otherwise form. Keep
pure reusable helpers in shared/Utils only when several systems use them;
server-only helpers stay in server/Utils, feature-specific helpers stay nearby.

## Components and canonical owners

A component ModuleScript returns function(registry), which creates its Component
class when ComponentService or ComponentController starts. Add the factory
require to the existing loader's factories array. Construct prepares instance
state, Start connects behavior, Stop cleans each instance, and update methods
own per-frame work. The supplied Spinner is the concrete client example.
Class destruction is a shutdown operation; do not assume it stops live instances.

Keep profile loading and saving in DataService, receipts in PurchaseService,
screens in UIController, and packet declarations in shared/Packets.luau. Do not
add another bootstrap, recursive loader, Knit framework, raw-remote hierarchy,
or parallel profile system merely to implement a feature. An intentional
architecture replacement requires an explicit project decision and migration.

## Review

Trace input to Packet to server validation to DataService mutation to Replica
to DataController to the screen. Check registry ordering, absence of cycles,
partial-startup cleanup, streaming component lifetimes, and server-only placement.
Load `roblox-networking`, `roblox-data`, and `roblox-luau-patterns` for their contracts.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
