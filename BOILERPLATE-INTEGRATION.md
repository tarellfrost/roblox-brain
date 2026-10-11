# Using the adapted skills

The rewritten skill files are ready to replace the same paths in your
tarellfrost/roblox-brain fork. No game source was changed, no tools were installed
globally, and the skills were not installed into an active agent session.

## Files and scope

The patch ZIP contains 32 rewritten Markdown files: eight named domain skills
and the other eight gameplay skills. It also includes this guide, provenance,
and both license notices. The full repository ZIP additionally contains the
other 13 unchanged skills, validators, tests, and an updated README whose install
commands point to your fork. That command will deliver this edition only after
you commit/push the replacement files to your fork.

## Game integration

Keep roblox-game-boilerplate as the game root with its existing AGENTS.md,
manifests, scripts, source, and vendor tree. Install/copy the complete adapted
skill directories, including references/full.md, through the host's existing
skill mechanism. Do not replace the game's AGENTS.md with Brain's maintenance
guide. If the libraries share a repository, scope Brain maintenance instructions
to the skill subtree and preserve the game's root architecture instructions.

Add a game-root instruction that skill examples must use the project's current
services/controllers, registries, Lifecycle, Packet, Replica, and data/purchase/UI
owners. User-selected exceptions remain possible; intentional framework changes
need an explicit migration rather than conflicting examples.

## Review and validation

Check VALIDATION.md for checks actually run. Brain's validator requires PyYAML
and luau-compile. Quick skill validation is structural, not evidence that a game
feature works. API/source drift checks require remote access. Existing API review
dates were preserved rather than falsely claiming live documentation review.
The boilerplate check.ps1/type/build and Studio playtest are required when a real
game implementation uses these examples; the rewrite changes documentation only.

Important snapshot limitations are documented in the relevant references:
the receipt ledger retains 100 IDs; grants must avoid partial mutation on error;
data acquisition has no explicit elapsed deadline in DataSessions; the full
profile is initially replicated to its owner; shutdown data listeners have a
special lifetime; and later asynchronous errors are outside Lifecycle's startup
error handler. These limitations were not silently repaired in game source.
