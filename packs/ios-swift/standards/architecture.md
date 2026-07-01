---
id: packs/ios-swift/standards/architecture
type: standard
layer: pack:ios-swift
scope: always
requires: []
overridable: true
version: 1
---

# Standard: App Architecture

**Default: MV (Model–View) with the Observation framework. First-party frameworks throughout. No third-party architecture layer.**

The reasoning, so deviation is an argument rather than a mood: `@Observable` collapsed most of what view models existed to do — SwiftUI views already bind directly to observable models with fine-grained invalidation. A ceremonial `*ViewModel` per screen is indirection without a job. And third-party architecture frameworks (TCA and kin) are powerful but fight SwiftUI's grain and age on their maintainers' schedule; for apps meant to live for years on Apple's platforms, Apple's primitives are the durable bet.

## The shape

- **Models:** `@Observable` domain objects + plain value types. Business logic lives here — testable without any UI.
- **Services:** protocol-fronted capabilities (persistence, import/export, networking), injected via `@Environment`. Protocols exist for *substitution in tests*, not speculative abstraction.
- **Views:** compose models and services; contain layout and interaction, not business rules. Logic worth unit-testing gets extracted *out* of the view — that's the testability line, not a view-model layer.
- **Persistence:** SwiftData by default for new stores; Core Data remains legitimate where SwiftData has gaps — that choice is per-app ADR material.
- A screen with genuinely complex presentation logic may earn a presentation object. That's an exception with a reason, not a pattern to propagate.

## Rules

- Each project records its architecture as **ADR-0001**, even when it just adopts this default — making it explicit is what makes later deviation reviewable.
- New third-party dependencies of any kind: plan-level checkpoint + ADR (per the [security standard](../../../kernel/standards/security-privacy.md)).
- The architect persona reviews against the *project's* ADRs first, this default second.
