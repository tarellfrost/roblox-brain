# Boilerplate Packet Networking: Full Reference

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

## Channel definitions

Inspect src/shared/Packet and Packets.luau rather than assuming another package's
API. Packet(name, ...types) declares an event; Response(...types) declares a
request/response. Add definitions to the shared Packets table during its normal
module loading. Server declaration after the first frame errors.

Illustrative addition to the existing table, not a replacement of Ping:

```luau
--!strict

local Packet = require(script.Parent.Packet)

local Packets = {
	Ping = Packet("Ping"):Response(Packet.NumberF64):RateLimit(5, 1),
	GetCash = Packet("GetCash"):Response(Packet.NumberF64):RateLimit(5, 1),
}

return Packets
```

GetCash illustrates response wiring; ordinary UI must use DataController's
replicated Cash instead of shipping this redundant query in a game.

## Service and controller integration

Illustrative handler at src/server/Services/BalanceService.luau, registered
after DataService in src/server/Registry.luau:

```luau
--!strict

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Packets = require(ReplicatedStorage.Packets)

local BalanceService = { Name = "BalanceService" }
local services: { [string]: any } = {}

function BalanceService.Init(_self: any, registry: { [string]: any })
	services = registry
end

function BalanceService.Start(_self: any)
	Packets.GetCash.OnServerInvoke = function(player: Player): number
		local data = services.DataService:GetData(player)
		return if data then data.Cash else 0
	end
end

function BalanceService.Destroy(_self: any)
	Packets.GetCash.OnServerInvoke = nil
end

return BalanceService
```

In real features extend the existing owner rather than creating BalanceService
solely for this specimen. A response Fire call yields; launch it from an owned
task or an interaction callback, not a lifecycle hook. Event subscriptions return
connections for Trove cleanup. Server event sends use FireClient(player, ...) or
FireAllClients(...); do not broadcast private player state.

## Security boundary

The sender passed to a server handler is the identity; ignore claimed player IDs.
Reject malformed strings, excessive lengths, unknown IDs, non-finite numbers,
out-of-range counts, forbidden targets, unavailable profiles, and impossible
state transitions. Recheck ownership, proximity, cooldown, and permissions on
the server. Prices and rewards come from server-owned catalogs. Keep validation
and mutation together without a yield that invalidates the checked state.

Bound work before expensive lookups or loops. Packet RateLimit is per channel
per player. Use Utils/RateLimit when an action spans packets; remove player
entries on departure. A channel limit is not an authorization or replay guard.
Do not impose punishments based on one malformed message.

## Responses, retries, and persistence

The supplied default response timeout is 10 seconds and timeout value is normally
nil unless configured. Excess requests receive no reply, so client debounce
prevents ordinary interactions reaching that limit. Distinguish false denial
from nil timeout, and do not assume timeout means the request was never applied.
For non-idempotent actions, reconcile state or implement a server-owned operation
ID ledger before automatically retrying. The Packet module is not that ledger.

DataSessions:WaitForSave can wait up to 25 seconds in this snapshot. A response
that waits for persistence must choose a longer timeout deliberately and define
what the client displays while the outcome is unknown. RequestSave only requests
work; WaitForSave checks LastSavedData using the supplied predicate.

## Actions versus state

Services mutate player data through DataService. DataController observes Replica
changes, including an initial load, so HUDs do not need polling or state packets.
Replica and Packet are separate channels: do not assume a packet arrives after
the replica change it describes. Use request IDs or versions if correlation is
required. Send private data to its owner; shared statistics need a separate
explicit replication contract. Measure bytes, send rate, and recipient count.

## Engine exceptions

The template does not select the engine Server Authority settings bundle. Preserve
the game's actual authority mode. Engine synchronized simulation input is a
separate mechanism from application Packet requests; introducing it requires
checking current engine documentation and settings. Do not infer unreliable
delivery, a generator API, or payload limits from another network library and
attribute them to this Packet implementation. A specialized transport needs an
explicit reason and project integration plan.

## Verification

Test accepted, denied, malformed, too-fast, duplicate, and timed-out requests;
profile-not-loaded and player-leave cases; and multiple clients. Check state
visibility and no second handler/remote owner. Load `roblox-security` for threat
analysis and `roblox-data` for durable mutations.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
