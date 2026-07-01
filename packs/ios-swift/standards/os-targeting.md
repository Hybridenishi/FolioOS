---
id: packs/ios-swift/standards/os-targeting
type: standard
layer: pack:ios-swift
scope: on-plan
requires: []
overridable: true
version: 1
---

# Standard: OS Targeting

**Policy: target N−1, track N.** Deployment target is the *previous* year's OS; the current beta is tracked and planned for, never required.

As of July 2026: **deployment target iOS 26**, tracking iOS 27 betas. Revisit every fall when the new OS ships (this document gets a pack minor bump; the concrete numbers here are the only part expected to change).

## Rules

- New-OS-only APIs ship behind `if #available(iOS <N>, *)` with a reasonable N−1 experience — a feature that silently vanishes on N−1 is a bug; degrade visibly or design the fallback.
- **Plan-time callout:** any plan step relying on an N-only API must say so, with its fallback, in the step's risk line. That's a product tradeoff the human approves, not an implementation detail.
- Beta SDKs never build release candidates. Track N in a branch or behind availability checks; release builds use the shipping SDK.
- Deprecation warnings from the current SDK are addressed in the release cycle they appear — that's the treadmill that keeps N−1 targeting cheap.
- Raising the deployment target is an ADR: note the user percentage being dropped and what capability is gained.
