# Boilerplate Player Data: Full Reference

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

## Existing ownership chain

DataService requires server-only ProfileStore, PlayerData, and DataSessions.
DataSessions starts, validates, migrates, reconciles, saves, and releases sessions.
DataService wraps each loaded profile data table in an owner-only PlayerData
Replica. DataController subscribes and exposes read/observe APIs on the client.
Extend these owners; do not install ProfileService/DataStore2 or create independent
player persistence alongside them.

## Schema evolution

PlayerData.luau contains exported Data, Template, Validate, and Migrate. Defaults
are reconciled by ProfileStore after migration. The supplied load order is
Validate -> Migrate -> Reconcile, so Validate must accept every historical schema
the migration supports. Simply increasing Template.SchemaVersion while rejecting
the old version prevents migration from running.

Illustrative replacement Migrate when moving from version 1 to version 2 with
a new Wins field; also update Data, Template, and Validate in the same change:

```luau
--!strict

local function migrate(data: any)
	local version = data.SchemaVersion or 1
	if version == 1 then
		if data.Wins == nil then
			data.Wins = 0
		end
		data.SchemaVersion = 2
	end
end

return migrate
```

This is a migration-function specimen, not an extra system or file to register.
Use its logic inside PlayerData.Migrate. Validate old and new representations,
reject unsupported future versions without resetting progress, and test missing
fields, corrupt types, and repeated migration. Serializability checks must cover
finite numbers, UTF-8 strings, table key shapes, and nested data. The supplied
validator is not automatically sufficient for new fields.

## Mutations and acknowledgements

Call services.DataService:Set(player, { "Wins" }, nextWins) for visible fields.
Use GetReplica for SetValues/TableInsert/TableRemove only after checking it is
available. Mutating GetData(player).Wins directly persists in memory but does
not notify the client. Direct writes are reserved for server bookkeeping such as
SchemaVersion, LastSeenAt, Receipts, and final release writes.

AdjustCash rejects non-finite/fractional deltas and balances outside its supported
range. It changes Replica before calling RequestSave; a false result must not be
treated as a general rollback guarantee. Define mutation, save-request, and
durable-acknowledgement semantics separately. For a saved-operation requirement,
WaitForSave's predicate must identify the specific operation in LastSavedData.
Checking only a coincidental balance is not proof of a particular grant.

After any yield, reacquire GetData and validate that the relevant session and
state still match. Do not keep an old profile table and write into it after the
player leaves or loses the session. Validate on the server before any mutation.

## Player lifetime and shutdown

Other services connect PlayerLoaded and PlayerReleasing in Start. DataService's
existing asynchronous load allows startup subscribers to attach; do not replace
this with per-feature PlayerAdded data loading. Features receive initialized
profiles through the canonical owner. Releasing callbacks must not yield.

DataService is first in the registry and last to Destroy. A final-write listener
must remain connected through that release even after its service Destroy.
Keep it out of the Trove that service Destroy clears, and retain any final state
it needs until release. The supplied Signals live for the process; this is a
shutdown convention, not a complete hot-reload strategy. If a feature requires
restartability, coordinate release and listener disposal explicitly.

## Client observations and privacy

DataController:Get(path?) returns nil until loaded. Observe(path, callback)
provides the initial loaded value and changes at, above, or below that path,
returning a disconnectable connection. Screens register it with Trove. Render
loading/disabled states instead of letting missing data imply zero authoritative
value. Returned tables are read-only client views.

The supplied entire data table, including bookkeeping fields, is sent to its
owner on initial load. Calling Receipts or SchemaVersion 'server bookkeeping'
does not make those values secret. Do not add secrets to this schema. Public
leaderboards need a separate explicit replica/attribute contract, not replication
of another player's entire profile.

## Storage and reliability limits

Studio uses a distinct PlayerData_Studio store; the template falls back to mock
data when datastore access is unavailable. Mock success does not prove durable
saves. Existing keys are Player_<UserId>; preserve them and user association.
Do not change identity APIs as an incidental feature edit.

DataSessions supplies a Cancel callback for acquisition based on shutdown/player
presence; the snapshot does not impose its own elapsed-time deadline. Review
the installed ProfileStore version before promising a bounded load time. Add
any acquisition deadline in this canonical owner, not a feature-level duplicate.
Cross-profile trades are not atomic merely because both profiles are session
locked; they need durable recovery records and idempotent operation semantics.

## Verification

Test schema upgrade, malformed data without reset, delayed join, leave during
load/save, session loss, owner-only replication, reconnect, and shutdown final
writes. Test durability in a published Studio place with API access and isolated
test data. Load `roblox-monetization` for receipt acknowledgement contracts.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
