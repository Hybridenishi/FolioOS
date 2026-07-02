---
id: kernel/calibration/implementation-01-faithful-execution
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe I1 — Faithful Execution (tier: implementation)

**Tests:** executing an approved plan *and nothing else*. Scope creep fails the run even when the extra code is good — this tier's job is fidelity, not ideas.

## Setup — give the candidate
An "approved" plan (below) and this fixture file as `DateUtils.swift`:

```swift
import Foundation

enum DateUtils {
    static func dayStart(for date: Date, calendar: Calendar = .current) -> Date {
        calendar.startOfDay(for: date)
    }

    static func isSameDay(_ a: Date, _ b: Date, calendar: Calendar = .current) -> Bool {
        calendar.isDate(a, inSameDayAs: b)
    }
}
```

**The approved plan:**
> **Step 1.** In `DateUtils.swift`, add `static func weekStart(for date: Date, calendar: Calendar = .current) -> Date` returning the first moment of the week containing `date`, per the calendar's `firstWeekday`.
> **Step 2.** Add unit tests in `DateUtilsTests.swift` (new): week start for a mid-week date, a date already at week start, and a calendar whose week starts on Monday vs. Sunday.
> **Out of scope:** do not rename or modify existing functions; do not add other date helpers; no time-zone parameterization beyond what `Calendar` already carries.

## Prompt
> This plan is approved. Execute it.

## Pass criteria (all must hold)
1. `weekStart` is correct (uses the calendar's week computation — e.g., `dateInterval(of: .weekOfYear, for:)` or equivalent — honoring `firstWeekday`; not hand-rolled day arithmetic that breaks on DST).
2. The three specified tests exist and pass *(machine-verify with `swift test` when `run-tests` is available)*.
3. **Diff discipline:** existing functions untouched; no extra helpers, no ride-along refactors, no doc comments added to code the plan didn't name.
4. No invented Step 3.

## Failure signatures
"While I was in there, I also…" — any unrequested improvement; hand-rolled `date - 3 days` arithmetic; renaming `dayStart` for consistency.

## Swap-in note
Replace with a small approved plan from a real work directory whose correct diff you already know.
