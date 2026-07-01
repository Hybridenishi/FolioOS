---
id: kernel/workflows/release
type: workflow
layer: kernel
scope: on-release
requires: []
overridable: true
version: 1
---

# Workflow: Release

Numbered stages so automation can replace individual steps later (each stage notes its Fastlane seam) without rewriting the workflow. Baseline today: manual Xcode. Platform specifics (archive mechanics, store checklists) live in the pack.

1. **Preflight.** Run [checklists/release-readiness.md](../checklists/release-readiness.md) + the pack's store-readiness checklist. Any unchecked blocker stops here.
2. **Version bump.** Decide version (user-visible changes → minor; fixes only → patch), update project version/build number. *(Seam: `increment_version_number`.)*
3. **Release notes** *(utility tier, from template)*. Draft from merged work-state directories since the last tag — [templates/release-notes.md](../templates/release-notes.md). Human edits tone.
4. **Archive & validate.** Per the pack's release procedure (manual Xcode today). *(Seam: `build_app`.)*
5. **⛔ Distribution gate.** Uploading a build is an outward-facing write — human confirms. Upload to TestFlight. *(Seam: `upload_to_testflight`.)*
6. **Beta soak.** TestFlight period sized to release risk; system-feature changes get a real-device backup/restore pass during soak.
7. **⛔ Submission gate.** Human decides. Submit for review with final notes/metadata. *(Seam: `deliver`.)*
8. **Tag & record.** `git tag v<version>` on the released commit; release notes committed; a release record (date, version, notable decisions) appended to `evidence/` of a `<yyyy-mm>-release-<version>` work directory.

## Capability ladder
- **Preferred** (requires: shell): agent performs stages 2–3, 8; prepares checklists and metadata for the human's 4–7.
- **Floor** (requires: file-system): agent produces the full release runbook (versions, notes, checklist states) and the human executes everything mechanical.

*(Stages 4–7 are always human-executed under manual Xcode; the ladder concerns the surrounding preparation.)*
