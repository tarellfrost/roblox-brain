# Boilerplate NPC and AI: Full Reference

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

Server state decides NPC targeting, combat, spawn limits, and rewards. Use an
NpcService for global orchestration and a server Component factory for repeated
tagged NPC lifetimes when that division fits the feature. Register the service
in server/Registry and factories in ComponentService. Client components/controllers
own cosmetic feedback, not AI outcomes.

Split sensing, state transitions, path logic, and tuning into child modules once
they are distinct concerns. Pure decisions receive state/configuration as inputs;
only the owning service/component starts updates and connections. Use explicit
idle/patrol/chase/attack/dead transitions to cancel stale movement and targets.

Pathfinding ComputeAsync yields, so run it in an owned asynchronous operation
outside lifecycle hooks. Guard completion with a generation token and recheck
the NPC/target/state. Handle failed paths, blocked movement, moving targets,
timeouts, and NPC removal; an old path result must not replace a newer plan.
Bound recomputation and stagger updates based on profiler evidence and NPC count.
Do not hide permanent loops in module-level code or spawn unlimited path requests.

Detection filters by distance before optional FOV and raycast line of sight.
Validate targets on the server and do not trust client claims that an NPC died
or should award currency. Choose physics authority consistent with the actual
project mode; classic network ownership calls are not a substitute for engine
Server Authority configuration.

Author rig/prefab assets in Studio. Respect EditorOnly relocation if a library
is stored in Workspace but accessed from ServerStorage after bootstrap. Spawned
clones need caps and cleanup. Award progress through DataService; use PlayerLoaded
for profile-dependent setup and preserve final-release listener lifetimes.

Stop disconnects NPC events and cancels path/movement work; Destroy releases
the global owner. The supplied component class Destroy is a shutdown operation,
not a guarantee that every live instance Stop has run. Design per-instance death,
streaming/removal, and despawn explicitly. Test unreachable targets, player leave,
NPC removal during path computation, respawn, duplicate deaths, spawn limits,
and multiple-client observation.

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
