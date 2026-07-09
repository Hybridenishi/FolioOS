# Adapter: codex — Reviewer Slice

The one usable piece of this adapter: Codex as external reviewer for the [cross-vendor-review workflow](../../kernel/workflows/cross-vendor-review.md). This slice deliberately needs almost nothing from a full adapter — the reviewer consumes a self-contained packet, so no compiled instructions, no harness integration, no MCP.

Slice version: 0.1.0 · Full adapter: still stub (see [README.md](README.md))

## Calibration gate

Before first production use, run [R3 adversarial-verify](../../kernel/calibration/reasoning-03-adversarial-verify.md) against the exact Codex model/config you'll use (3 runs, per the rubric). Record the result in a calibration record. A reviewer that can't refute the bogus findings doesn't get a vote.

## Invocation

From the packet directory, non-interactively (verify the CLI's current flags — `codex exec` as of mid-2026; pin the model explicitly so runs are comparable):

```sh
cd .folioos/.scratch/packet-<slug>/
codex exec --model <pinned-model> "$(cat PROMPT.txt)" > report-raw.md
```

`PROMPT.txt` is one of the two fixed prompts below with `<mode>` specifics filled from PACKET.md. **Do not improvise the prompt** — prompt drift makes runs incomparable across the adoption experiment.

## Fixed prompt — independent review

> You are an external code reviewer. You have no prior context; the packet in this directory is everything. Read PACKET.md first, then review the subject (`diff.patch` and/or `plan.md`, with `context/`) against the standards in `standards/`.
>
> Find what is wrong or missing. For each finding: severity (blocker | should-fix | nit), file:line, a one-sentence defect statement, and a concrete failure scenario — specific inputs or state leading to a specific wrong outcome. No style opinions a standard doesn't back. If you find nothing at a severity, say so explicitly. Do not summarize the change; review it.

## Fixed prompt — findings verification

> You are an external verifier. You have no prior context; the packet in this directory is everything. Read PACKET.md, then adversarially test each finding in `findings.md` against `diff.patch` and `context/`: attempt to refute it.
>
> Per finding: CONFIRMED (with a concrete failure scenario) or REFUTED (with the mechanism that makes the finding wrong). Assign your own severity to confirmed findings; ignore any severity in the input. Then add findings of your own that the list missed, same format. Hedged verdicts are not verdicts.

## Filing the report

Copy the raw output verbatim to the work directory: `evidence/<yyyy-mm-dd>-crossvendor-codex.md`, with a header block: packet slug, mode, model + CLI version, invocation date. Then append the adoption-experiment footer:

```markdown
---
Experiment: did any cross-vendor delta change a decision? <pending human review>
```

The receiving agent annotates around the report (delta classification happens in the synthesis), never inside it.
