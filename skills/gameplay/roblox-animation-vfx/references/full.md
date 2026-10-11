# Boilerplate Animation and VFX: Full Reference

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

Put game-wide client effects in an existing controller or EffectsController;
put animation behavior tied to a tagged instance in a Component factory. Combat
services own hit decisions and damage. Packet notifications trigger cosmetic
feedback; client animation markers never grant damage or persistent rewards.

Store track/effect resources under a character, component, or effect-lifetime
owner. Load animations through the rig's Animator, choose priorities deliberately,
and reconnect for a replacement character. Track marker connections and transient
attachments/instances in Trove; stop tracks and cancel tweens when their owner
ends. Do not keep old character tracks after respawn.

Use burst emitters, bounded lifetime, and pooling only when frequent allocation
shows a measured cost. Attachments define beam/trail endpoints; release both
generated endpoints and effects. Screen fades belong to a UIController screen;
camera shake is an operation on the camera owner, not another writer competing
for CurrentCamera.

Design warning, impact, and fade so gameplay remains readable. Preload critical
assets in owned asynchronous work and handle invalid/inaccessible assets without
blocking Start. An effect that never finishes needs a fallback timeout as well
as a completion event. Predicted effects must be reversible on rejection; if
the game explicitly uses rollback simulation, verify its animation APIs instead
of assuming ordinary cached-track behavior applies.

Test respawn, missing assets, interrupted playback, repeated impacts, streaming
removal, and teardown. Profile particle overdraw, lights, and allocations on the
target device. The server owns outcomes even if an effect looks successful.

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
