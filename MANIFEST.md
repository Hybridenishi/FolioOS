# FolioOS Release Manifest

The tested combination of component versions. Projects pin a manifest release, not individual components.

## Release 0.1.0 — 2026-07-01

| Component | Version | Notes |
|---|---|---|
| kernel | 0.1.0 | Initial contracts, principles, personas, standards, workflows, templates, checklists |
| pack: ios-swift | 0.1.0 | SwiftUI-first, Swift concurrency, MV/@Observable default, iOS 26 target / iOS 27 tracking |
| adapter: claude-code | 0.1.0 | Full capability manifest + manual compile procedure |
| adapter: codex | stub | Capability sketch only; not usable |
| adapter: gemini | stub | Capability sketch only; not usable |

Compatibility rule: an adapter at major version N supports kernel major version N. Pre-1.0, treat every minor bump as potentially breaking and read the changelog before updating a pinned project.
