# Boilerplate Purchases and Entitlements: Full Reference

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

## 3. Centralize Developer Product receipts

PurchaseService/init.luau assigns MarketplaceService.ProcessReceipt in Start
and clears it in Destroy. It finds the player and DataService profile, checks
Receipts[purchaseId], executes the matching Products handler, records the ID,
prunes the ledger, and calls WaitForSave with a saved-receipt predicate.
It returns PurchaseGranted only after the snapshot check succeeds; otherwise
NotProcessedYet. Do not replace this with a skill's standalone callback or a
second BindReceiptHandler dispatcher.

Add an entry to src/server/Services/PurchaseService/Products.luau. Illustrative
grant for a product whose configured ID is 123456789; replace it with the actual
developer-product ID and requested reward before use:

```luau
--!strict

export type Grant = (player: Player, services: { [string]: any }) -> boolean

local Products: { [number]: Grant } = {
	[123456789] = function(player: Player, services: { [string]: any }): boolean
		return services.DataService:AdjustCash(player, 500)
	end,
}

return Products
```

Preserve existing handlers when adding entries. Prices/product IDs come from
trusted configuration and platform data, not client arguments. Prompt from the
screen/controller with PromptProductPurchase; do not turn its completion event
into a grant signal.

## 4. Grant and durability contract

Grants must not yield, prompt, wait on a client, or call WaitForSave themselves.
The owner records the receipt immediately after the callback returns, allowing
reward and receipt to be saved together. No-yield code alone is insufficient:
a callback that mutates and then throws/returns false can leave a reward without
the ledger entry. Validate everything before mutation, keep commit paths narrow,
and avoid additional fallible steps after applying the reward.

AdjustCash mutates before requesting a save. Audit failure semantics when writing
multi-step or custom rewards; do not assume false means no state was changed.
For more complex grants, define an atomic in-memory commit or rollback plan
within the canonical data owner before using the existing callback contract.

RequestSave is not an acknowledgement. The receipt owner uses WaitForSave to
check LastSavedData for the purchase ID. A failure/timeout remains pending and
must not trigger a fresh unrelated grant. Do not promise exactly-once behavior
solely because the callback exists. Simulate interruptions and verify recovery.

## Bounded ledger and unsupported reward shapes

The snapshot retains the newest 100 receipt IDs. An older pruned ID is no longer
deduplicated by this ledger. Larger/permanent retention is a deliberate data
design change, not a guarantee supplied by the template. Test replay around
pruning and decide whether the reward type requires stronger retention.

The flow protects one player's profile-backed reward and receipt record. Gifts
to other profiles, global events, spawned world rewards, and external effects
cannot be made atomic merely by recording a buyer's receipt. They need durable
operation records, idempotent consumers, and a recovery owner. Explain those
limitations before implementing such a requested reward.

## Passes, subscriptions, and eligibility

Game passes are durable entitlements, not developer-product receipts. Check
UserOwnsGamePassAsync server-side, handle failure, refresh ownership after a
purchase, and expire per-player caches when appropriate. Clients may display
an offer but do not decide eligibility or unlock privileges.

For subscriptions, paid random items, regional restrictions, and platform
programs, inspect current official APIs/policies at implementation time. Do not
apply stale prices, quota formulas, or geographic assumptions from a general
example. Keep policy checks narrowly on the affected purchase path; preserve
unrelated gameplay. Creator/platform rewards are not game receipt grants.

## Verification

Test duplicate receipts, player absent, profile unavailable, callback false/error,
partial mutation, player leaving during save, durable acknowledgement failure,
shutdown, ledger pruning, and unknown products. Check there is one receipt owner
and that product rewards reach the owner's Replica/UI. Use isolated test products
and data; `roblox-data` defines the persistence boundary.

## Provenance

Adapted from the supplied roblox-brain-main.zip (MIT, copyright 2026 TabooHarmony)
and roblox-game-boilerplate-master.zip (Unlicense). Boilerplate contracts and
examples were checked against the supplied local source on 2026-10-11; external
engine APIs were not revalidated live during this adaptation. Selected original source
links remain in SKILL.md as documentation pointers, not as a claim of new review.
When the template changes, inspect the actual implementation before relying on
these snapshot-specific paths, hooks, timeouts, and shutdown behavior.
