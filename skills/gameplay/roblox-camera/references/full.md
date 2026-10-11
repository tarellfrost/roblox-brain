# Boilerplate Camera: Full Reference

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

Use one CameraController for the local view. If it grows, keep init.luau as
the registered owner and put Modes/Smoothing/Types in child modules. Screens
request a cutscene or mode through context.Controllers.CameraController rather
than requiring it directly. Other systems request camera effects through the
registry; they do not write CurrentCamera independently.

Start acquires the current camera and attaches an owned visual update connection
or named render binding. Destroy disconnects/unbinds and restores the state it
changed where the same camera is still owned. Track camera replacement, character
respawn, and subject availability. Capturing CurrentCamera once at module load
leaves stale references when it changes.

For a fully controlled camera, choose Scriptable deliberately and restore prior
type/subject/FOV on exit. Update CFrame and Focus coherently. Use CFrame.lookAt
and explicit local/world transforms; smoothing should use dt rather than a
fixed frame-dependent interpolation factor. If ordering relative to engine
camera work matters, use an explicitly named render binding with cleanup.

Cutscenes need a mode/context owner that cancels tweens, restores input/mouse
state, and ends on skip, respawn, missing targets, or shutdown. A server may send
a validated cutscene event via Packet; it does not author each client's camera.
The camera is presentation, not a movement permission or loading gate.

Screen and viewport ray APIs use different coordinate conventions. Match the
input origin to the ray API and multiply unit-ray direction by the desired
cast length. A placement ray yields a client preview; server rules validate
the requested target. Exclude the local character and preview where appropriate.

Screen shake composes into the camera owner's final transform, not a competing
render loop. Restore FOV and mouse locks only for the context that changed them.
Verify touch/gamepad control, occlusion, resize, respawn, camera replacement,
cutscene interruption, and cleanup. Studio inspection does not prove Play behavior.

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
