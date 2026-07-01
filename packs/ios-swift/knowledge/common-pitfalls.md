---
id: packs/ios-swift/knowledge/common-pitfalls
type: knowledge
layer: pack:ios-swift
scope: on-demand
requires: []
overridable: true
version: 1
---

# Knowledge: Common Pitfalls

Failure patterns worth pattern-matching against during build and review. **This file is the knowledge-promotion loop's primary landing zone** — real incidents from projects graduate to entries here. Entries an OS release invalidates get deleted at the WWDC-cycle pack review.

## SwiftUI
- **Unstable `ForEach` identity.** `id: \.self` on non-unique/mutable values causes ghost rows, broken animations, state attached to the wrong item. Identity must be stable and unique.
- **State attached to a view that gets recreated.** `@State` resets when a parent applies `.id()` or restructures — "my toggle keeps resetting" is usually an identity change upstream.
- **`onAppear` as a lifecycle guarantee.** It can fire multiple times (or not when expected) in lazy containers and navigation. Idempotence or `.task` (which also cancels) is the fix.
- **Whole-screen invalidation from over-broad observation.** A giant observable "app state" object touched by every view invalidates everything on any change. Split models along actual dependency lines.
- **`GeometryReader` greed.** It takes all proposed space — wrapping content in it "to get the size" reflows the layout around it.

## Concurrency
- **Unowned `Task { }` outliving its screen.** Fire-and-forget tasks writing to deallocated-screen state; structured concurrency or `.task` scoping is the fix, not `[weak self]` reflexes.
- **Actor reentrancy.** State checked before an `await` inside an actor may be stale after it — re-validate, don't assume atomicity across suspension points.
- **Main-thread hops hidden in loops.** An innocent `await MainActor` call per item in a large loop serializes everything through the main thread.

## Persistence & data
- **Migration tested only from the latest schema.** Users upgrade from *every* shipped version, including the one from two years ago. (See system-feature QA.)
- **Dates without time zones / calendars.** "Same day" comparisons that break across DST or for users abroad; always compute via `Calendar`, store absolute instants.
- **Fetch-all-then-filter in memory** where a predicate belongs — fine at 100 records, molasses at 10k. System-feature QA's "large" shape exists to catch this.

## Process
- **The demo-path-only feature.** Import that handles the file exported five minutes ago, but not the empty/huge/older-version one. Hostile shapes are in the QA checklist because this keeps happening.
