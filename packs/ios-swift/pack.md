---
id: packs/ios-swift/pack
type: knowledge
layer: pack:ios-swift
scope: on-demand
requires: []
overridable: false
version: 1
---

# Pack: ios-swift

Platform knowledge for native Apple-platform apps. Layered on the kernel by the cascade; adds standards, checklists, and knowledge — overrides nothing in the kernel at this version.

**Positions this pack takes** (each is an ADR-able default — projects deviate via override + ADR, not silently):

- **SwiftUI-first**, UIKit as a justified escape hatch — [standards/swiftui-conventions.md](standards/swiftui-conventions.md)
- **async/await structured concurrency**, no new completion-handler code — [standards/swift-concurrency.md](standards/swift-concurrency.md)
- **MV + `@Observable`**, first-party frameworks, no third-party architecture layer — [standards/architecture.md](standards/architecture.md)
- **Target N−1, track N**: currently target iOS 26, track iOS 27 betas — [standards/os-targeting.md](standards/os-targeting.md)

**Maintenance rhythm:** this pack turns over with the platform. Each WWDC cycle: re-verify the knowledge files against current HIG/APIs, revisit os-targeting, bump the pack minor. Knowledge entries that an OS release invalidates are deleted, not annotated.

Contents: `standards/` (4) · `checklists/` (swiftui-review, hig-review, app-store-readiness) · `knowledge/` (hig-cheatsheet, common-pitfalls).
