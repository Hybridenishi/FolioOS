# Calibration — 2026-07-09

## Candidate
- Model: gpt-5.5 (Codex default; reported in run metadata)
- Harness: Codex CLI 0.143.0, `codex exec` non-interactive
- Config: vendor default sampling; prompt via arg with stdin closed (`< /dev/null` — CLI otherwise blocks reading stdin)
- Ensemble: no

## Scope
Reviewer-slice qualification only (cross-vendor-review workflow prerequisite): R3 adversarial-verify, 3 runs. Full tier placement not attempted — this candidate is qualified as *external reviewer*, not placed in the routing tiers.

## Runs (P = pass, F = fail)
| Probe | Run 1 | Run 2 | Run 3 | Notes |
|---|---|---|---|---|
| R3 adversarial-verify | P | P | P | Both bogus findings refuted with correct mechanisms in all runs; real findings 2 & 3 confirmed with concrete scenarios in all runs; run 2 additionally confirmed finding 1 with a valid duplicate-value scenario; runs 1 & 3 refuted finding 1 with defensible static-snippet reasoning (criteria require 2 of 3 real confirmed — met) |

Judge: Claude (Fable-class), session of 2026-07-09; criteria applied verbatim from the probe. Candidate did not judge its own runs.

## Placement
- **Qualified: external reviewer** (cross-vendor-review workflow) ✅
- Raw outputs: `../../.scratch/calibration-codex-r3/run{1,2,3}-raw.md` (scratch; this record is the durable evidence)
- **Valid until:** Codex CLI major change, model change from gpt-5.5, or repeated real-work underperformance
