# Boilerplate Lighting and Atmosphere: Full Reference

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

## Ownership and implementation

Author the base Lighting, Atmosphere, Sky, and post-processing setup in Studio
and save the place. Rojo does not own that authored environment in this template.
Inspect the existing mood and gameplay visibility before changing settings.
Verify which engine properties are runtime writable; do not set Studio-only
renderer settings from a generated Script.

Use a service for authoritative day/round state and a controller for local
presentation such as zone tint, underwater effects, or camera-local feedback.
Shared timing/mood parameters can live in a frozen Config table. Keep one owner
for each animated Lighting property so a cycle, cutscene, and zone effect do not
silently overwrite one another.

For a synchronized day cycle, replicate compact timing/state changes through
an explicitly defined state contract rather than broadcasting ClockTime every
frame. The client derives its display from the agreed time base. Packet is for
cycle transition events; do not mirror a state Replica that already exists.
Global non-player state is distinct from the private PlayerData replica.

Connect updates in Start and release them in Destroy; permanent unowned while
true loops are inappropriate. Cancel a previous transition tween before starting
its replacement. Track created effects with Trove and restore baseline state
when leaving a local zone/context, including character removal or camera change.

Local tagged lamps can use Component factories. Validate brightness/range/color
attributes, and distinguish gameplay state from cosmetic flicker. Server rules
must not depend on a client-local lighting effect. Do not add a component for a
static authored lamp with no behavior.

Tune effects to the actual art direction, not universal numeric presets. Check
readability, shadow/light cost, bloom/overdraw, and low graphics settings on the
target devices. Test repeated zone entry, overlapping influences, respawn,
transition cancellation, clock wraparound, and stopping the owning system.

## Integration sequence

1. Inspect the current place/assets and existing feature owners; extend one when it already owns the concern.
2. Select a service, controller, component, or pure child using the ownership rules above.
3. Add only the required registry/factory/screen entries. Put client-sendable channels in shared/Packets.luau with rate limits.
4. Connect and schedule during Start; keep Init and feature requires free of yielding work and runtime side effects.
5. Track instances, events, tasks, tweens, and context restoration under their actual lifetimes.
6. Run scripts/check.ps1 for code changes and the boundary-specific playtest described above. Save authored place changes.

## Related contracts

Use `roblox-architecture` for system/child boundaries, `roblox-luau-patterns` for
resource ownership, `roblox-networking` for Packet validation, `roblox-data` for
profile mutations, and `roblox-gui` for screens. These do not authorize replacing
the project's chosen framework or making unrelated game-design decisions.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
