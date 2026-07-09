---
id: kernel/templates/review-packet
type: template
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Template: Cross-Vendor Review Packet

A self-contained directory handed to an external reviewer that has **no session context and cannot ask questions**. Everything it needs is in the packet; anything not in the packet doesn't exist. Assemble under `.folioos/.scratch/packet-<slug>/` (the packet is scratch; the *report* it produces is evidence).

```
packet-<slug>/
├── PACKET.md            # this manifest — the reviewer reads it first
├── plan.md              # copy of the plan (independent-review of a plan/diff)
├── diff.patch           # the change, `git diff` output (when reviewing a diff)
├── findings.md          # ONLY in verification mode — the findings to refute/confirm
├── context/             # minimal supporting sources: the files the diff touches,
│                        #   in full, post-change; relevant type definitions
└── standards/           # copies of the standards the review should judge against
```

## PACKET.md manifest

```markdown
# Review Packet: <slug>

- **Mode:** independent-review | findings-verification
- **Subject:** <one sentence: what this change/plan does>
- **Risk class:** <why this earned cross-vendor review — migration / system feature / release>
- **Contents:** <list each file and what it is>
- **Judge against:** <the standards/ files, by name>
- **Out of scope:** <what the reviewer should NOT spend effort on>
- **Required output format:** findings with severity (blocker | should-fix | nit),
  file:line, one-sentence defect, concrete failure scenario — or, in verification
  mode, per-finding CONFIRMED (with scenario) / REFUTED (with mechanism).
```

## Anchoring rules (the part that keeps the second opinion independent)

- **Independent-review mode:** the packet **excludes** the first agent's findings, review synthesis, and commit-message rationale. The plan is included (the reviewer must know intent) but nothing that says what the first reviewer concluded.
- **Verification mode:** `findings.md` contains the findings verbatim, stripped of severity consensus and synthesis narrative — the reviewer re-derives severity itself; inherited severity is anchoring.
- Either mode: never include "we think this is fine because…" framing. The packet states facts and asks questions.

## Self-containment check (before handing off)

- [ ] Could a competent stranger with only this directory do the review? (No repo paths that dangle, no "see the discussion" references.)
- [ ] Every file the diff touches is present post-change in `context/`
- [ ] The output format is stated — unformatted opinions can't enter the synthesis
