---
id: kernel/workflows/model-calibration
type: workflow
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Workflow: Model Calibration

How an arbitrary model set (any vendor, open-source, local) gets placed into the [model-routing](../principles/model-routing.md) tiers. You're not ranking models in general — you're qualifying them for roles: **place each candidate at the highest tier whose probes it passes; route work to the cheapest qualifier for the tier the task needs.**

## Protocol

1. **Shortlist.** Use public benchmarks only to pick candidates worth testing (coding leaderboards, instruction-following, long-context). They can't measure Swift fluency or contract obedience — the probes do that.
2. **Freeze the configuration.** You calibrate **(model × harness × config)**, not a model name: the agent harness it runs in, quantization (local models), context window, sampling settings. A different config is a different candidate. **Ensembles and routers (MoA stacks, cascades) calibrate as a single black box in their deployment config** — member models are irrelevant to placement.
3. **Run the probes** in [kernel/calibration/](../calibration/): 3 probes per tier, **3 runs each** (variance is itself a signal — a flaky pass is not a pass). Start every candidate at the utility tier and work up; a candidate that fails a lower tier doesn't test higher ones.
4. **Grade** each run binary pass/fail against the probe's criteria, per the [rubric](../calibration/rubric.md). Your strongest available model judges against the written criteria; a human spot-checks. **A model never judges its own runs.**
5. **Place.** Qualification per tier: no probe fails all 3 runs, and ≥ 7 of 9 probe-runs pass. Highest qualifying tier = placement.
6. **Record** the dated tier-mapping table in the relevant adapter's `capabilities.md`. Placements are perishable — they carry a date, always.

## Recalibration triggers

- The vendor updates the model (silently or otherwise) or you change harness, quant, or context config.
- A placed model repeatedly underperforms in real work — real-work failure outranks a probe pass; demote first, re-probe second.
- New tier-relevant probes get promoted from `candidates/` (a real incident that a tier should have caught is exactly the kind of learning the promotion loop exists for).

## Keeping probes honest

- Swap the starter fixtures for ones drawn from your real projects as soon as you have them — probes from your own history measure fit, not trivia.
- Probes leak into training data over time if published; keep project-derived fixtures private, rotate any probe that a model family suddenly aces suspiciously.

## Capability ladder
- **Preferred** (requires: shell, run-tests): implementation probes verified by actually building/running tests on the candidate's output.
- **Floor** (requires: file-system): all probes run as document exchanges; implementation outputs graded by review against the pass criteria, noted as "not machine-verified" in the results table.
