---
id: kernel/calibration/utility-03-format-exact
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe U3 — Format-Exact ADR (tier: utility)

**Tests:** byte-level template compliance when transcribing a decision into the record — the least glamorous, most drift-prone job the utility tier does.

## Setup — give the candidate
[kernel/templates/adr.md](../templates/adr.md) and this decision summary:

> Decided 2026-07-15, during work `2026-07-attachments`: new attachment storage uses **files on disk referenced by the SwiftData model**, not BLOBs in the store. Reasons: store size and backup speed degraded badly in testing with photo-heavy entries (200MB store from ~300 photos); file references keep the store lean and let backups stream. Considered BLOBs-in-store (simpler consistency, one file to back up — rejected on the measured performance) and a hybrid cache (rejected: complexity without a demonstrated need). Consequence: attachment files and store can now theoretically desync; a consistency check runs at launch. Revisit if SwiftData ships external-storage attributes with acceptable performance.

## Prompt
> Write this as ADR-0011 in `.folioos/decisions/0011-attachment-storage.md`, following the template exactly.

## Pass criteria (all must hold)
1. **Structure matches the template exactly:** title as assertive phrase, the four metadata bullets (Date / Status / Work), and the four sections in order — Context, Decision, Alternatives considered, Consequences.
2. All facts placed in the right sections (the 200MB measurement is *context*; the launch consistency check is a *consequence*; both alternatives appear with their why-nots).
3. The **revisit trigger** appears in Consequences, per the template's instruction.
4. `Status: accepted`; date correct; work directory linked.
5. **Nothing invented, nothing dropped** — every fact from the summary lands somewhere; no editorial additions.

## Failure signatures
Renamed or reordered sections; merged alternatives into prose; invented a "Superseded by" it wasn't given; dropped the revisit trigger; added recommendations the decision summary doesn't contain.
