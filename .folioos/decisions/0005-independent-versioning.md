# ADR-0005: Independent component versions, manifest-pinned

- **Date:** 2026-07-01
- **Status:** accepted

## Context
Kernel principles change rarely; the iOS pack turns over every WWDC; adapters change on vendor schedules. A single version couples these clocks, forcing fake bumps and making updates scarier than they are.

## Decision
Kernel, each pack, and each adapter carry their own semver (`VERSION` files); `MANIFEST.md` records tested combinations; projects pin a manifest release. Methodology semver: major = contract changes, minor = standards/workflow changes, patch = clarifications. The CHANGELOG is written as a methodology diff.

## Alternatives considered
- **Single repo version:** simple but couples clocks; an os-targeting tweak shouldn't look like a methodology change.
- **Separate repos per component:** maximal decoupling, absurd overhead for a solo maintainer.

## Consequences
One extra indirection (manifest) at update time, paid for by honest signals about what actually changed. Pre-1.0: contracts may still shift; 1.0 is declared when the contracts survive a few months of real use unchanged.
