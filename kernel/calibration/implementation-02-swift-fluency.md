---
id: kernel/calibration/implementation-02-swift-fluency
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe I2 — Swift Fluency (tier: implementation)

**Tests:** idiomatic modern Swift against the pack standards — the probe where models strong in Python/JS but thin on Swift wash out.

## Setup — give the candidate
[packs/ios-swift/standards/swiftui-conventions.md](../../packs/ios-swift/standards/swiftui-conventions.md) and [swift-concurrency.md](../../packs/ios-swift/standards/swift-concurrency.md), plus:

```swift
struct Entry: Identifiable, Sendable {
    let id: UUID
    let title: String
    let date: Date
}

protocol SearchService: Sendable {
    func search(term: String) async throws -> [Entry]
}
```

## Prompt
> Build `EntrySearchModel` (observable model) and `EntrySearchView` (SwiftUI). Requirements: as the user types, search via the injected `SearchService`; a new search **cancels the in-flight one**; show results in a list; handle the loading, empty-results, and error states. Include a preview using a stub service.

## Pass criteria (all must hold)
1. Model is `@Observable` and `@MainActor`; no ObservableObject/@Published, no view-model ceremony beyond the one model.
2. **Cancellation actually works:** in-flight search cancelled on new input (stored `Task` cancelled, or `.task(id:)` keyed on the term); a cancelled search must not overwrite newer results; `CancellationError` isn't rendered as a user-facing error.
3. Concurrency standards hold: async/await only, no completion handlers, no `Task.sleep` polling; cancellation checked/propagated.
4. View standards hold: `ForEach`/`List` identity via `Identifiable` (no `id: \.self`); async work in `.task`/`.onChange` not `.onAppear + Task`; loading/empty/error states all rendered; `#Preview` present with the stub.
5. Compiles *(machine-verify when `shell` is available; otherwise judge against criteria and mark not-machine-verified)*.

## Failure signatures
UIKit-flavored Swift (delegates, completion blocks); a `DispatchQueue` sighting; debounce via `Task.sleep` *without* cancellation of the prior search; error state that swallows cancellation as failure.

## Swap-in note
Keep the spec stable but rotate the domain; the criteria are the durable part.
