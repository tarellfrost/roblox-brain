# Boilerplate Toolchain and Source Ownership: Full Reference

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

## Existing toolchain

The supplied aftman.toml pins Rojo, Wally, StyLua, wally-package-types, and
luau-lsp. Keep these reviewed project choices. A newer tool manager or alternative
package ecosystem is a separate migration, not a prerequisite to a feature.

Run from the game root:

```powershell
./scripts/install.ps1
rojo serve default.project.json --address 127.0.0.1
```

Connect Studio's Rojo plugin to localhost:34873. Once a place is selected, its
servePlaceIds configuration should refer to that actual place; never invent an
ID. The install script downloads definitions if needed, runs Wally, generates
the sourcemap, and repairs package types. Check the current script on disk for
its exact sequence. First-time tool trust and network failures may require user
action; do not declare setup complete from partial output.

## Packages and version discipline

Existing shared packages: Component, Input, Signal, TableUtil, Trove, WaitFor.
ProfileStore is server-only. Packet lives in src/shared, and Replica is vendored
under vendor/Replica. Search these APIs before adding another dependency.
Do not add Janitor, Knit, a network framework, or a second state replication
package to duplicate existing owners without an intentional project decision.

Use the existing Wally realm sections, run install.ps1, and commit wally.toml
and wally.lock together. Manifest version text alone is not proof of the
installed version; inspect the resolved lockfile. Do not manually edit generated
package files or delete the lockfile to hide a dependency conflict. Read the
resolver error and fix the actual constraint/realm mismatch.

Replica's supplied fixed roots are ReplicatedStorage.ReplicaClient,
ReplicatedStorage.ReplicaShared, and ServerScriptService.ReplicaServer. Preserve
them and its Apache-2.0 license. Updating vendor is an explicit upstream-version
replacement plus checks/playtest, not a local feature patch to library internals.

## File/Studio ownership

default.project.json maps src/shared directly to ReplicatedStorage, src/server
to ServerScriptService, and src/client to StarterPlayerScripts. Refer to
ReplicatedStorage.Packets, not ReplicatedStorage.Shared.Packets. Mapped code is
edited on disk; mapped folders are Rojo-owned. Do not assume live Studio script
changes are synchronized back into files.

World geometry, Lighting, and StarterGui are place-owned. Save the place after
authoring them. Rojo's code build is not the complete game and must not replace
the authored place. If Studio and files disagree, inspect both before overwriting
anything. EditorOnly tags move authoring-only Workspace content to ServerStorage
before system startup; never use them on objects gameplay expects in Workspace.

## Checks and evidence

```powershell
./scripts/check.ps1
```

This supplied script formats src with StyLua, regenerates sourcemap.json, runs
luau-lsp analysis with definitions, and builds build/code.rbxlx. It writes files;
review formatting changes. It requires installation first. Do not substitute a
plain Rojo build for the type/lint checks. For CI, deliberately add a check-only
format gate if desired; the current script itself is not check-only.

Static checks cannot prove Studio assets exist, receipts persist, input works on
devices, or multiplayer authority is correct. Add a focused playtest for the
changed boundary. Report checks actually run and any unresolved prerequisite.

## Maintaining the skills alongside the game

Brain skills keep category/name/SKILL.md and references/full.md. Preserve full
directories on installation, attribution, source metadata, valid cross-links,
and the repository's size limits. The Brain root AGENTS.md describes maintaining
a skills library; the game root AGENTS.md describes implementing games. Do not
replace the latter with the former when copying skills into the game.

Run validate_skills.py and its unit tests in the Brain tree. Luau code-fence
syntax checking needs luau-compile; Roblox-aware type analysis needs installed
game packages and actual integration files. API/source drift checks need network
access. A missing prerequisite is an unrun check, not a reason to silently weaken
the validator. Do not bump last_reviewed as if remote APIs were reverified when
only local boilerplate contracts were inspected.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
