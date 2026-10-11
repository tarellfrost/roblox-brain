# Boilerplate Localization: Full Reference

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

Keep authored translation tables and localizable UI in Studio/platform tooling
and save the place. Screen modules remain under UIController; do not add a
separate LocalScript per label. Stable translation keys belong in the existing
screen's configuration or a shared Config module when several screens use them.

Use automatic localization for authored GUI where it fits. For formatted or
dynamic messages, obtain a translator asynchronously with explicit failure
handling and fallback source text. Cache within the appropriate player/screen
lifetime; do not yield in a controller's Init/Start. Guard completion so a late
translator result cannot update a destroyed screen.

If translation is shared across screens, a LocalizationController can expose
the project's translation operation and readiness signal through the registry.
If only one screen needs it, keep a child module instead. Use the existing
Signal/Trove packages for readiness subscriptions and cleanup. Do not invent
an engine method for locale refresh; inspect the current installed API.

Format dynamic values through defined translation entries rather than joining
English fragments. Check source/fallback text, placeholders, pluralization
requirements, long translations, wrapping, scrolling, and gamepad navigation.
DataController supplies confirmed values to display; localization never mutates
the client's replicated profile. Persisted language preferences require server
validation and DataService, not an independent save system.

Locale is not a trustworthy country or purchase eligibility proof. If a requested
feature needs platform region/policy APIs, check current official contracts,
handle failure, and keep the decision in a server owner. Do not derive Roblox
product prices from an IP-country example or treat client locale as entitlement.
Avoid logging personal region information unless required by the feature.

Test initial translator delay/failure, missing keys, changing displayed values,
repeated screen open/close, character respawn, destruction before completion,
and representative short/long/non-Latin translations. The module implementation
and actual authored table entries must both exist before claiming localization
is finished.

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
