---
id: kernel/calibration/utility-02-release-notes
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe U2 — Release Notes (tier: utility)

**Tests:** two-audience writing from structured inputs — the template's tone split, honest fix wording, completeness without invention.

## Setup — give the candidate
[kernel/templates/release-notes.md](../templates/release-notes.md) and these inputs:

> **App:** Folio 2.4.0, date 2026-07-15. Deployment target iOS 26. Xcode 26.4.
> **Merged work since 2.3.1:**
> - `2026-06-on-this-day`: feature — an "On This Day" section on the home screen surfaces past entries from the same date; empty until the user has year-old entries. ADR-0009 recorded (date bucketing approach).
> - `2026-07-import-emoji-fix`: fix — importing an archive containing entries whose titles start with an emoji duplicated those entries. Regression test added.
> **Known issue shipping:** On This Day ignores entries created before the v2.0 data migration (planned for 2.4.1).

## Prompt
> Draft the release notes per the template.

## Pass criteria (all must hold)
1. **Template structure exact** — both sections, correct header line, all "For the record" fields present including deployment target/Xcode.
2. **User half:** benefit-first, plain language ("See what you wrote on this day in past years…"); the fix stated honestly per the template's rule ("Fixed an issue where importing a backup could duplicate entries…") — no implementation vocabulary (no "emoji-prefixed titles", no "regression test").
3. **Record half:** both work dirs linked, the ADR listed, the known issue present verbatim in substance.
4. **Nothing invented:** no thanks-for-feedback boilerplate claims, no features that aren't in the inputs, no promised 2.4.1 date beyond what's given.
5. The empty-state caveat of On This Day is *not* in the user-facing half as an apology, but the known issue *is* recorded internally — the probe checks the candidate can route facts to the right audience.

## Failure signatures
Marketing fluff invention; leaking dev vocabulary to users; dropping the known issue; restructuring the template.
