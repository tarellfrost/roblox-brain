---
name: roblox-monetization
description: "Use for Roblox boilerplate PurchaseService grants, durable receipts, game passes, subscriptions, and purchase eligibility."
last_reviewed: 2026-08-21
sources:
  - https://create.roblox.com/docs/reference/engine/classes/MarketplaceService
  - https://create.roblox.com/docs/reference/engine/classes/MarketplaceService#BindReceiptHandler
  - https://create.roblox.com/docs/production/monetization/passes
  - original
  - https://raw.githubusercontent.com/tarellfrost/roblox-game-boilerplate/master/AGENTS.md
---

# Boilerplate Purchases and Entitlements

## When to Load

Load for this domain in a game based on tarellfrost/roblox-game-boilerplate.
Explicit user choices and the current project AGENTS.md take precedence; use
the supplied architecture rather than general-purpose examples from another framework.

## Quick Reference

- PurchaseService is the sole ProcessReceipt owner. Add developer-product grants to its Products.luau table; do not introduce another receipt dispatcher.
- Products grants receive (player, services), mutate only the profile through DataService, do not yield, and return true only after the grant is applied.
- Client prompts are presentation. Never grant from PromptProductPurchaseFinished or a client Packet request.
- Existing receipt flow records the ID with the reward and checks a saved snapshot before PurchaseGranted. Preserve this order.
- Unknown products, missing profiles, and unconfirmed saves remain pending; do not acknowledge them as granted.
- The template keeps only 100 receipt IDs. This is bounded deduplication, not permanent exactly-once coverage.
- Game passes use server-checked ownership and belong outside Products. Subscription entitlement needs its own current platform contract.
- Paid gifts, cross-profile grants, and world effects need explicit durable idempotency/recovery beyond the existing single-profile flow.
- Verify current platform APIs and eligibility rules when implementing policy-sensitive or new monetization paths.

> Implementation contracts and examples: [references/full.md](references/full.md).
