---
id: kernel/checklists/release-readiness
type: checklist
layer: kernel
scope: on-release
requires: []
overridable: true
version: 1
---

# Checklist: Release Readiness

Stage 1 of the release workflow. Platform-agnostic half; the pack's store-readiness checklist covers the rest.

- [ ] `main` is green: full test suite passes on the release commit
- [ ] All work directories in this release are `status: done` (no `executing` stragglers)
- [ ] No open `blocker` findings in any included review synthesis
- [ ] System-feature changes in this release re-verified on a real device
- [ ] Version and build number bumped; matches the release-notes header
- [ ] Release notes drafted — both halves — and human-edited
- [ ] Docs tree has no stale pages covering shipped code (writer's staleness check run)
- [ ] Known issues list is honest and written down
- [ ] Previous version's data upgrades cleanly to this build (install old → upgrade → verify)
