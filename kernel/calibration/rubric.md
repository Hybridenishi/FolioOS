---
id: kernel/calibration/rubric
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Calibration Rubric & Results Record

Copy per calibration run. Grading is binary per probe-run: every listed pass criterion holds, or the run fails — "close" is a fail with a note.

## Judge rules
- Judge = strongest available model, given the probe's pass criteria verbatim + the candidate's output. Never the candidate itself.
- Human spot-checks at least one run per tier per candidate.
- The judge classifies; the *criteria* decide. If the judge wants to pass a run the criteria fail, the criteria win (then file a candidate to improve the criteria).

## Results record

```markdown
# Calibration — <yyyy-mm-dd>

## Candidate
- Model: <name + exact version/checkpoint>
- Harness: <agent tool + version>          <!-- placement is (model × harness × config) -->
- Config: <quant, context window, sampling; "vendor default" is a valid answer>
- Ensemble: <no | yes — describe topology; calibrated as one unit>

## Runs (P = pass, F = fail, — = not run)
| Probe | Run 1 | Run 2 | Run 3 | Notes |
|---|---|---|---|---|
| U1 doc-regeneration | | | | |
| U2 release-notes | | | | |
| U3 format-exact | | | | |
| I1 faithful-execution | | | | machine-verified? y/n |
| I2 swift-fluency | | | | machine-verified? y/n |
| I3 regate-discipline | | | | |
| R1 trap-plan | | | | |
| R2 pushback | | | | |
| R3 adversarial-verify | | | | |

## Placement
- Utility: qualified / failed (rule: ≥7/9 runs pass, no probe fails all 3)
- Implementation: qualified / failed / not tested
- Reasoning: qualified / failed / not tested
- **Placed at:** <tier> · **Cost note:** <per-Mtok or local; is it the cheapest qualifier at this tier?>
- **Valid until:** recalibration trigger (model/harness/config change) — see workflow
```

Completed records live in the FolioOS repo under `.folioos/work/<yyyy-mm>-calibration-<model>/evidence/`; the resulting tier table goes in the adapter's `capabilities.md`, dated.
