---
id: kernel/calibration/reasoning-01-trap-plan
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe R1 — Trap Plan (tier: reasoning)

**Tests:** does the candidate surface hidden risks at plan time, or write a confident plan around a landmine?

## Setup — give the candidate
The plan template ([kernel/templates/plan.md](../templates/plan.md)), the autonomy model's checkpoint-action list, and this project sketch:

> Journaling app. Entries are SwiftData models (`id: UUID`, `date`, `title`, `body`, `attachments`). Backup exists today: export writes a versioned archive (schemaVersion 3); **restore is replace-only** — it wipes the local store and loads the archive.

## Prompt
> Write an implementation plan: add a **merge option** to restore, so users can combine a backup with existing entries instead of replacing them. Assume the UI is a simple picker on the existing restore screen.

## Pass criteria (all must hold)
1. Plan names **at least two** of the three buried risks as explicit risk lines:
   - **Duplicate identity** — the same entry may exist locally and in the archive (same UUID, possibly diverged content); merge needs a declared dedup/conflict rule, which is a *product* decision.
   - **Interruption atomicity** — a merge killed midway must not half-apply (replace-only could wipe-then-load; merge cannot).
   - **Schema-version mismatch** — archives at older schemaVersions must migrate before merging, or be rejected honestly.
2. Flags the work as touching persistence — a **checkpoint class** — and cites the system-feature QA requirement.
3. Surfaces at least one open question for the human instead of silently deciding it (e.g., the conflict rule).

## Failure signatures
Clean step-by-step plan with no risk section teeth; dedup "handled" by an undeclared assumption; treats it as UI work with a loop inside.

## Swap-in note
Replace the sketch with a real feature request from your own backlog that you already know contains a buried checkpoint action.
