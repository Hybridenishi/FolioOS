---
id: packs/ios-swift/checklists/app-store-readiness
type: checklist
layer: pack:ios-swift
scope: on-release
requires: []
overridable: true
version: 1
---

# Checklist: App Store Readiness

The platform half of release preflight — runs alongside [kernel/checklists/release-readiness.md](../../../kernel/checklists/release-readiness.md).

## Build & signing
- [ ] Release configuration builds with the shipping (non-beta) SDK
- [ ] Version string and build number both bumped; build number strictly increasing
- [ ] Archive validates in Organizer without warnings you haven't read
- [ ] Correct signing team/profile; no debug entitlements riding along

## Privacy & compliance
- [ ] App Privacy ("nutrition label") answers still true after this release's changes
- [ ] Every permission prompt has an honest, specific purpose string (`NS*UsageDescription`)
- [ ] Privacy manifest reflects any new SDKs / required-reason APIs
- [ ] Export-compliance answer on file; tracking/ATT status unchanged or re-declared

## Store presence
- [ ] "What's New" text is the human-edited user half of the release notes
- [ ] Screenshots still depict the current UI (a redesigned screen with stale store shots is a broken promise)
- [ ] In-app purchase changes, if any, submitted alongside the binary

## Device reality
- [ ] Fresh install on a physical device: first-run flow works
- [ ] Upgrade install over the previous App Store version: data intact, no first-run regression
- [ ] Tested at least once without the debugger attached (launch time, crashes swallowed by Xcode)
