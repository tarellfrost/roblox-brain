# Boilerplate Physics and Constraints: Full Reference

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

Use server services for authoritative vehicle/projectile/ragdoll rules,
components for repeated tagged mechanisms, and controllers for client input or
prediction. Register systems/factories in the existing registries/loaders. Do
not attach independent Scripts to each vehicle or introduce a parallel loader.

Author rigs, constraints, attachments, and collision geometry in Studio. Runtime
generated attachments/forces/projectiles belong to an explicit owner and Trove
or component Stop cleanup. Pure CFrame/trajectory/validation calculations belong
in child modules; shared calculations contain no trusted mutable gameplay state.

Inspect constraints by their actual required attachments, actuator mode, limits,
force/torque, mass, and anchoring. Some actuator constraints support one-attachment
modes; do not apply a universal 'two attachments always required' rule. Use the
current engine API for the selected constraint rather than a copied generic rig.
Test overconstraint jitter, collision groups, and ownership under realistic load.

Vehicle controllers send bounded intent through Packet. Server handlers validate
seat/ownership, ranges, state, and rate; they do not accept an arbitrary position,
speed, or force as proof. Classic client-owned assemblies remain untrusted for
gameplay outcomes. If engine Server Authority is explicitly selected, verify its
settings and synchronized input mechanism instead of equating SetNetworkOwner
with that model.

Server combat owns hit/raycast decisions, projectile expiry, damage, and rewards.
Client tracers are cosmetic. Validate finite vectors, distance, cooldown, origin,
and target state. Bound fast-projectile collision with an appropriate swept test
rather than assuming Touched catches every case. Define duplicate-hit guards
and lifetime caps; do not persist transient projectile transforms every frame.

Ragdoll activation tracks exactly which motors it disabled and constraints it
created. Recovery restores those changes and handles death/respawn/destruction.
Moving platforms and elevators have one server movement owner; client camera
compensation belongs to CameraController rather than another camera script.

Test high/low frame rates, network delay, unauthorized input, ownership changes,
collision, duplicate contacts, dismount, respawn, removal during updates, and
cleanup. Confirm Studio assets and modules are both present; static checks alone
do not prove a constraint assembly behaves correctly.

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
