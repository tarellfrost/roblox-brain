---
name: roblox-data
description: "Use for DataService, ProfileStore sessions, Replica mutations, PlayerData schema changes, migrations, and save acknowledgement."
last_reviewed: 2026-10-02
sources:
  - https://create.roblox.com/docs/reference/engine/classes/DataModel
  - https://create.roblox.com/docs/cloud-services/data-stores
  - https://create.roblox.com/docs/cloud-services/data-stores-vs-memory-stores
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Player Data

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- DataService owns player profiles; DataSessions wraps session operations. Other services use registry.DataService, never a second store or save loop.
- Update Data, Template, Validate, and Migrate in src/server/Data/PlayerData.luau together. Preserve old-schema readability during migrations.
- GetData can return nil. Never keep its table across a yield; reacquire and revalidate the active session afterward.
- Use Set, AdjustCash, or GetReplica mutation methods for client-visible fields. Direct table writes bypass Replica updates.
- RequestSave requests persistence; WaitForSave checks a saved snapshot. Never report durable completion from RequestSave alone.
- Client code uses DataController:Get/Observe/IsLoaded; reads are immutable. Add Observe connections to a screen/controller Trove.
- Player setup uses PlayerLoaded in Start; final bookkeeping uses non-yielding PlayerReleasing. Preserve those release listeners until DataService shuts down.
- Studio store is PlayerData_Studio; live is PlayerData. Existing keys use Player_<UserId>. Preserve identity and key formats unless explicitly migrating them.
- Ordered stores, world state, and cross-server coordination belong to roblox-server-data, not a second player-profile owner.

> Implementation contracts and examples: [references/full.md](references/full.md).
