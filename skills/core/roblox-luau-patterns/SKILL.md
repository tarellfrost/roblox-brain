---
name: roblox-luau-patterns
description: "Use for Luau modules, Trove cleanup, Signal events, scheduling, and lifecycle ownership in the Roblox boilerplate."
last_reviewed: 2026-10-02
sources:
  - https://luau-lang.org/
  - https://luau.org/library
  - https://create.roblox.com/docs/reference/engine/classes/ObjectValue
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Luau Patterns for the Boilerplate

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Use the project's service/controller lifecycle for system owners, plain modules for pure helpers, and objects for independent instance lifetimes.
- Preserve --!strict and existing naming: PascalCase public methods, _camelCase private fields, camelCase locals.
- Direct requires are for pure helpers, packages, and children. Cross-system access uses the registry captured in Init.
- Use existing Trove and Signal packages instead of introducing duplicate cleanup or event libraries.
- Set up state in Init; connect, bind, and schedule in Start; release owned resources in Destroy. Component instances use Stop.
- No feature connections, instances, or yielding operations at module load. Do not parallelize ordered lifecycle hooks.
- Track spawned tasks, guard asynchronous completion after destruction, and keep operation errors observable.
- Distinguish a thrown failure from a successful nil result and an operation's documented false return.
- DataService PlayerLoaded/PlayerReleasing listeners need their special shutdown lifetime; do not put them in an ordinary service Trove blindly.

> Implementation contracts and examples: [references/full.md](references/full.md).
