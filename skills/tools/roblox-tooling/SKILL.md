---
name: roblox-tooling
description: "Use for the Roblox boilerplate Rojo, Wally, Aftman, strict type checking, formatting, builds, and skill validation."
last_reviewed: 2026-10-02
sources:
  - https://raw.githubusercontent.com/Roblox/creator-docs/main/content/en-us/reference/engine/classes/Players.yaml
  - https://rojo.space/docs/
  - https://wally.run/
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Toolchain and Source Ownership

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Follow the existing aftman.toml, wally.toml/lock, default.project.json, and PowerShell scripts. Do not migrate tools as a side effect of game work.
- Run scripts/install.ps1 on setup and after dependency edits; it installs tools, Wally dependencies, and package types. Bare wally install misses required steps.
- Run scripts/check.ps1 after code changes: it formats src, generates sourcemap, analyzes with luau-lsp, and builds code. Report its results accurately.
- Commit manifest and lockfile changes; inspect resolved versions. Generated Packages, ServerPackages, sourcemap, build output, and downloaded types are not hand-edited.
- Preserve vendored Replica and its fixed mappings/license; do not substitute a Wally fork or edit vendor code during feature work.
- Code is disk-authored and Rojo-synced; Workspace, Lighting, StarterGui, and art are Studio-authored and saved in the place.
- The build is code-only. Static checks cannot validate authored UI/world assets or multiplayer runtime behavior.
- For skill edits, run Brain's structural validator and focused tests; record unavailable compiler/network checks rather than calling them passed.

> Implementation contracts and examples: [references/full.md](references/full.md).
