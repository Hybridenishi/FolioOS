---
id: kernel/standards/git-workflow
type: standard
layer: kernel
scope: always
requires: []
overridable: true
version: 1
---

# Git Workflow Standard

**Model: trunk + short-lived branches, PR-gated.** `main` is always releasable.

- One branch per unit of work, named `feat/<slug>`, `fix/<slug>`, or `chore/<slug>`, matching the work-state directory slug.
- No direct commits to `main`. Everything lands via PR, after the [review pipeline](../workflows/review-pipeline.md) has run.
- Branches live days, not weeks. A branch that outlives its plan's scope means the plan was too big — split it.
- **Commits:** imperative subject ≤ 72 chars; body explains *why* when the diff doesn't. Commit at coherent checkpoints (compiles, tests pass), not per-file.
- **Agents commit only when the plan says so or the human asks.** Pushing, opening PRs, and tagging are outward-facing writes — checkpoint actions under the [autonomy model](../principles/autonomy-model.md).
- PR descriptions follow [templates/pr-description.md](../templates/pr-description.md) and link the work-state directory.
- Releases are tagged `v<version>` from `main` (see [workflows/release.md](../workflows/release.md)).
- History is not rewritten on shared branches. Squash-merge PRs to keep `main` linear and readable.
