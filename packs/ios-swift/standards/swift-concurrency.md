---
id: packs/ios-swift/standards/swift-concurrency
type: standard
layer: pack:ios-swift
scope: always
requires: []
overridable: true
version: 1
---

# Standard: Swift Concurrency

- **async/await only in new code.** No new completion handlers, no new Combine pipelines for plain async work (Combine stays legitimate for genuine multi-value streams). Wrap legacy callback APIs with continuations at the boundary, once, in one place.
- **UI state is MainActor.** `@Observable` models driving views are `@MainActor` unless there's a measured reason otherwise. Don't sprinkle `MainActor.run` — home the type correctly instead.
- **Structured over unstructured.** Child tasks and `async let` before `Task { }`; every unstructured `Task` needs an owner and a cancellation story. Fire-and-forget tasks are leaks with extra steps.
- **Cancellation is honored, not assumed.** Long operations check `Task.isCancelled` / call `Task.checkCancellation()` at sensible intervals; cleanup paths run on cancellation.
- **Strict concurrency checking on** (Swift 6 mode where the toolchain allows). Warnings are addressed by *fixing isolation design* — `@unchecked Sendable` and `nonisolated(unsafe)` are code-review blockers without a defended invariant comment.
- **Actors guard mutable shared state**; don't reach for locks in new code. Keep actor methods small — an `await` inside an actor is a reentrancy point; re-validate state after it.
- **No `Task.sleep` polling** for something observable — observe it (AsyncSequence, `.onChange`, notifications-as-streams).
