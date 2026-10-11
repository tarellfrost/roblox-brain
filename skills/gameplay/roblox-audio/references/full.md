# Boilerplate Audio: Full Reference

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

Keep music, UI feedback, and listener presentation in an existing controller
or AudioController. Tagged world emitters can use client components. A server
service decides when a shared gameplay cue should happen and notifies intended
clients through Packet; do not create standalone Scripts under every sound.

Choose the experience's existing audio system. Sound/SoundGroup and modular
AudioPlayer/Wire graphs are separate engine options; do not rebuild a working
mix solely to follow a generic preference. Author sound templates and mix
objects in Studio, save the place, and keep IDs/tuning in Config where public.
Runtime cloned sounds or graphs need explicit lifecycle owners.

For Sound playback, define looping, volume, rolloff, and emitter placement rather
than accepting defaults. An Attachment/BasePart provides a spatial source;
music/UI feedback is non-positional. Connect completion before Play when cleanup
depends on it. Track one-shots and add a fallback expiry for failed loads or
stopped playback; Ended alone is not a universal cleanup guarantee. Trove destroys
the owner's sounds and subscriptions at teardown.

Music needs one transition/mixing owner so controllers do not fight over volume.
A settings screen calls that controller through context.Controllers; it does
not require another system directly. Device-local preferences can be local;
persisted settings must go through a validated Packet and DataService mutation.
Do not accept arbitrary client-provided asset IDs for server broadcasts.

Preload essential audio asynchronously, surface failure, and avoid waiting
inside Start. Cap overlapping frequent sounds. Validate asset permission and
availability before describing the feature as complete. Test volume settings,
distance attenuation, respawn, failed loads, rapidly repeated cues, controller
destruction, and multiple clients hearing only intended events.

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
