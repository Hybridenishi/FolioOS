---
id: packs/ios-swift/checklists/swiftui-review
type: checklist
layer: pack:ios-swift
scope: on-review
requires: []
overridable: true
version: 1
---

# Checklist: SwiftUI Review

Applied by the staff-ios-engineer persona to UI-touching diffs.

## State & data flow
- [ ] State homed at the right level (`@State` local / `@Observable` shared / `@Environment` app-wide); no binding chains three levels deep
- [ ] No side effects in `body`; async work in `.task` (cancellation-aware), not `.onAppear` + `Task`
- [ ] Observable models driving UI are `@MainActor`
- [ ] `List`/`ForEach` identity is stable and unique — no `id: \.self` on non-unique values

## Structure
- [ ] Oversized `body`s decomposed into named `View` types
- [ ] No screen-wrapping `GeometryReader`; no `AnyView` where `@ViewBuilder`/generics suffice
- [ ] UIKit bridges isolated, each with its "why not SwiftUI" comment
- [ ] `#Preview` present and compiling for changed nontrivial views, incl. one non-default state

## Robustness
- [ ] Empty, loading, and error states exist — not just the happy path
- [ ] User-facing strings in String Catalogs; no hardcoded English in view code
- [ ] Colors/icons from asset catalog + SF Symbols; both appearances checked
- [ ] No `Task.sleep`-based UI timing hacks
