# Boilerplate Building and World Assets: Full Reference

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

Inspect the authored place, Workspace tree, existing assets, and Rojo mapping
before construction. Static maps, terrain, props, Lighting, and GUI are Studio
assets and must be saved in the place. They do not become durable merely because
a tool created them, and the code-only Rojo build does not contain the full map.

Define a named root model, pivot/origin, dimensions, player route, and asset
sources. Use relative CFrames and explicit anchoring, collision, material, and
shadow choices. Build in reviewable phases, read back hierarchy and bounds, then
inspect the camera view and traverse in Play. A successful command is not proof
that the world is navigable or visually correct.

Attach repeated behavior with Component factories under server/Components or
client/Components, registered in the existing component loaders. Tags discover
behavior and attributes hold validated instance tuning. Doors/pickups/hazards
do not each need a new Script. Global construction/spawning rules belong to a
service. A placement preview belongs to a controller and sends intent through
Packet; the server validates catalog, ownership, collisions, range, and cost.

Runtime procedural construction is appropriate when requested by the design.
Keep pure geometric calculations in child modules, clone/build from the service
or component lifecycle, and track created instances for cleanup. Persistent
placement records use DataService serializable IDs/transforms, not saved Instances
or a second datastore. Never trust a client CFrame without finite/bounds checks.

EditorOnly is specifically for authoring-only Workspace content that the game
reads from another location. Bootstrap moves it to ServerStorage before startup;
do not tag a runtime object that systems still look up in Workspace.

Inspect imported scripts and dependencies before insertion. Record provenance
and applicable license; use existing approved assets before generating replacements.
Do not assume Studio Assistant features are exposed as MCP tools: inspect available
capabilities. If Studio access is missing, deliver disk-side modules and describe
remaining map work rather than claiming an unobserved build.

Verify spawns, traversable paths, collision, scale, bounds, duplicates, streaming,
component registration, cleanup, and saving the place. Keep art and authoritative
game logic under their separate owners.

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
