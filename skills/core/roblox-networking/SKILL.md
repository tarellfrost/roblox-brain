---
name: roblox-networking
description: "Use for Packet channels, server validation, rate limits, Replica state, and request failure handling in the Roblox boilerplate."
last_reviewed: 2026-10-02
sources:
  - https://create.roblox.com/docs/scripting/events/remote
  - https://create.roblox.com/docs/scripting/security/security-tactics
  - https://create.roblox.com/docs/scripting/security/client-server-boundary
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Packet Networking

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- Declare application channels in src/shared/Packets.luau using the existing Packet module. Do not create channels inside Start or after the first server frame.
- Services bind OnServerInvoke or connect OnServerEvent in Start; clear handlers or disconnect in Destroy. Controllers/screens send intent through Fire.
- Chain RateLimit(count, window) on every client-sendable channel. Use shared/Utils/RateLimit for limits spanning channels or targets.
- Validate every payload against server-owned types, finite ranges, permissions, ownership, distance, cooldown, and current state. Packet schemas are not security.
- Packet carries actions/events. Replica carries state; client player data is read through DataController. Do not mirror existing replicas with packets.
- Response channels time out after 10 seconds by default; rate-limited requests are dropped without a reply. Handle timeout separately from denial and success.
- RequestSave is not a durable acknowledgement. Save-dependent replies need the existing WaitForSave contract and suitable ResponseTimeout.
- Existing raw-remotes or engine simulation input may be exceptions; do not add a second network framework by default.
- Workspace attributes are replicated hints, not proof of client authority.

> Implementation contracts and examples: [references/full.md](references/full.md).
