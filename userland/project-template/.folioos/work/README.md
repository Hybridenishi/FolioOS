# Work-State

One directory per unit of work: `<yyyy-mm>-<slug>/` containing `plan.md` (the state machine) and `evidence/` (append-only). Governed by [kernel/contract/work-state.md](../../../../kernel/contract/work-state.md) — read it before touching anything here.

The rules that matter most, restated because they're the contract's teeth:
- Only a human writes `status: approved` (+ `plan_hash`).
- Agents verify the hash before executing; mismatch = stop and re-gate.
- Evidence is append-only. Scratch goes to `.folioos/.scratch/` (gitignored), never here.

Approving a plan by hand:
```sh
# hash everything below the frontmatter's closing ---
awk 'f; /^---$/ && NR>1 {f=1}' plan.md | shasum -a 256
# then set status: approved, approved_by, approved_at, plan_hash in the frontmatter
```
