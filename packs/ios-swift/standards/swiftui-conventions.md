---
id: packs/ios-swift/standards/swiftui-conventions
type: standard
layer: pack:ios-swift
scope: always
requires: []
overridable: true
version: 1
---

# Standard: SwiftUI Conventions

**SwiftUI-first.** It carries Apple's long-term investment. UIKit is a legitimate escape hatch when SwiftUI genuinely can't do the job — wrapped in `UIViewRepresentable`/`UIViewControllerRepresentable`, isolated to its own file, with a comment stating *what SwiftUI limitation forced it* (that comment is the future deletion trigger when SwiftUI catches up).

## Views
- Small views, extracted freely — a `body` past ~50 lines or three levels of nesting wants decomposition. Extract to named `View` types (compiler-checked, previewable), not `@ViewBuilder` funcs, unless trivially local.
- Prefer standard controls and modifiers over custom lookalikes; custom UI needs a plan-level justification (the HIG checklist will ask).
- Layout with the standard stack/grid system; `GeometryReader` is a last resort and never wraps whole screens.
- Every nontrivial view keeps a working `#Preview`, including one non-default state (empty, error, or max Dynamic Type).

## State
- State lives at the lowest level that needs it: `@State` for view-local, `@Observable` model objects for shared, `@Environment` for app-wide services. Passing bindings three levels deep signals the state is homed too low.
- Views are cheap, disposable value types — no work in initializers; side effects go in `.task`/`.onChange`, not `body`.
- `.task` over `.onAppear` for async work — you get cancellation on disappear for free.

## Strings & assets
- User-facing strings go through String Catalogs from day one — retrofitting localization is misery.
- SF Symbols before custom icons; asset-catalog colors (light/dark aware) before hardcoded values.
