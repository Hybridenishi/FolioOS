---
id: kernel/checklists/system-feature-qa
type: checklist
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Checklist: System-Feature QA

For import/export, backup/restore, migrations, and sync — features whose failures cost user *data*, not just user patience. Unit tests never satisfy this checklist; these scenarios run against the real feature with real data shapes.

## Data shapes (run the feature against each)
- [ ] Typical: realistic content and volume
- [ ] Empty: zero-content input (new user, empty archive)
- [ ] Large: 10–100× typical volume — measure, don't just survive
- [ ] Malformed: truncated, corrupted, wrong file type entirely
- [ ] Wrong version: produced by an older app version; and a *newer* one if the format is shared
- [ ] Edge content: emoji, RTL text, extreme dates, maximum-length fields

## Interruption & repetition
- [ ] Kill the app mid-operation → relaunch → state is coherent (resumed, rolled back, or clearly reported — never half-applied)
- [ ] Run the operation twice → no duplication, no data loss
- [ ] Storage-full and permission-denied paths show honest, actionable errors

## Round-trip integrity
- [ ] Export → import on a clean install → **diff the data**, don't eyeball it
- [ ] Restore *over existing data* → merge/replace behavior matches what the UI told the user
- [ ] Field-level fidelity: timestamps, ordering, attachments, metadata all survive

## Migrations specifically
- [ ] Upgrade path from every *shipped* schema version, with realistic data
- [ ] Failed migration leaves the pre-migration store intact and recoverable

Results recorded in the QA-run record, per shape/scenario, including not-run rows.
