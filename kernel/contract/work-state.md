---
id: kernel/contract/work-state
type: contract
layer: kernel
scope: always
requires: []
overridable: false
version: 1
---

# Work-State Contract

The plan→approve→execute contract only works across agents, sessions, and vendors if approval is an **artifact, not a conversational event**. This contract defines that artifact and its lifecycle. Any agent, in any session, answers "am I authorized to execute this?" by reading a file — never by trusting chat history.

## Directory layout (per project)

```
.folioos/
├── work/                          # committed, machine-readable work-state
│   └── 2026-07-import-feature/    # <yyyy-mm>-<slug>, one dir per unit of work
│       ├── plan.md                # THE state machine (frontmatter below)
│       └── evidence/              # append-only: review reports, QA runs, screenshots
├── decisions/                     # ADRs — durable, append-only, dated
├── candidates/                    # learnings awaiting promotion (see knowledge-promotion)
├── overrides/                     # project-layer override documents
└── .scratch/                      # gitignored orchestration debris
```

Three lifecycles, never mixed: **work-state** (mutable frontmatter, committed), **evidence** (append-only, committed), **scratch** (gitignored).

## Plan state machine

`plan.md` frontmatter:

```yaml
---
id: work/2026-07-import-feature
type: plan
status: approved          # draft | approved | executing | blocked | done | abandoned
created: 2026-07-01
approved_by: nate         # human identifier; never an agent
approved_at: 2026-07-02
plan_hash: "sha256:3f9a…" # hash of everything BELOW the frontmatter at approval time
---
```

```
draft ──(human approves; hash recorded)──▶ approved ──▶ executing ──▶ done
  ▲                                            │             │
  └────────(plan revised; hash cleared)────────┴── blocked ◀─┘
                                                (re-gate)      or ▶ abandoned
```

## Rules (non-negotiable; `overridable: false`)

1. **Only a human sets `status: approved`** and the approval fields. An agent writing them is a contract violation.
2. **Execute only on hash match.** Before executing, the agent recomputes the hash of the plan body. Mismatch means the plan changed after approval → treat as `draft`, stop, re-gate. This makes post-approval edits *detectable*, not honor-system.
3. **The re-gate rule.** If execution surfaces a risk not in the approved plan (touching persistence, deleting beyond planned scope, a new dependency, an outward-facing write), the agent sets `status: blocked`, records why in the plan body under `## Re-gate log`, and stops. Resuming requires a fresh human approval of the revised plan.
4. **Evidence is append-only.** Review reports, QA runs, and screenshots go to `evidence/` with dated filenames. Never edited, never deleted; superseded evidence is superseded by newer files.
5. **Approved means the approved plan.** Execution follows the plan without re-litigating settled choices. Discovering a *better idea* mid-execution is a candidate for `candidates/`, not a license to deviate.
6. One unit of work, one directory. If scope splits, close the plan (`done` or `abandoned`) and open new ones.

## Computing the hash

`shasum -a 256` over the plan file content below the frontmatter's closing `---` (exclusive), byte-exact. Recorded as `sha256:<hex>`. The procedure is deliberately manual-friendly: a human can approve a plan with one shell command and a text edit.
