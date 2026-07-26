---
id: packs/foundry-vtt/knowledge/api-surfaces
type: knowledge
layer: pack:foundry-vtt
scope: on-demand
requires: []
overridable: true
version: 1
---

# Knowledge: API Surfaces

Where the Tier A boundary actually is, by capability — the map you need before deciding whether a feature is a Tier A write or a Tier B interpretation. Deliberately names *kinds* of surface rather than exact signatures: signatures move between system minors, and a stale signature list is worse than none.

## The rule that determines which surface to use

For any state change, prefer the **most specific system-owned entry point** that exists for it. A generic document update that happens to produce the right stored value is not equivalent to the system's own method for that change: the method applies rules, fires hooks, and lets automation modules react. Specificity descends:

1. A system method for exactly this operation
2. A system method for the general category
3. Core document API
4. Raw protocol mutation — legacy only

## Surfaces by capability

- **Damage and healing** — the system's damage-application method, not direct hit-point arithmetic. It consumes temporary pools and applies resistance in the right order; arithmetic does neither. If typed damage is not wired through, the reported numbers are wrong against a resistant or immune target, and wrong quietly.
- **Conditions and statuses** — the system's status-toggle method. Watch for conditions that are graded rather than boolean; treating a level as a flag silently clamps it.
- **Concentration-style tracked effects** — usually their own mechanic rather than a generic condition, and useful as a read long before as a write.
- **Rests and recovery** — the system's rest methods, invoked with dialogs suppressed and any player choice passed as an explicit input. No human is present to answer a prompt; a method that blocks on one hangs.
- **Combat and initiative** — the combat document's own turn and initiative methods. Computing the next turn yourself and writing the result desynchronizes every module watching turn changes.
- **Item and activity execution** — the system's use/activate method. This is the surface automation modules wrap, so calling it is what makes them fire. It is also the surface whose completion semantics are hardest to observe — see below.
- **Resources and charges** — system methods for slots and limited uses. Watch for alternate pools that summary views filter out; a pool you cannot see is a pool you will forget to decrement.
- **Compendium search** — index-first. Declare the fields you need up front, filter on the index, and load full documents as an explicit second step. Loading thousands of documents to filter three fields is the mistake this exists to prevent.
- **Stable references** — a document's universally-unique identifier is the handle to cite in receipts, links, and logs. Names are not identifiers.
- **Visibility and ownership** — per-user ownership maps are the native mechanism, and blanket visibility versus per-player secrets are the same mechanism at different settings. See [visibility-review](../checklists/visibility-review.md).

## Where completion semantics get hard

When an automation module wraps an execution surface, "did this finish, and what happened" stops being answerable from the return value: the workflow is asynchronous and may involve player-facing dialogs that never resolve. Budget effort there rather than on the API coupling, which is the easy part. Reporting a wrapped execution as complete because the call returned is the failure this note exists to prevent.

## What does not belong on this map

Reimplementing system rules; generating every field of a complex document from scratch; unrestricted macro execution. Each is a place where the platform's own surface exists and is being avoided — and the reason it is being avoided is usually that the specific method is inconvenient, which is not a reason.
