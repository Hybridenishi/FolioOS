# Work-State

One directory per unit of work: `<yyyy-mm>-<slug>/` containing `plan.md` (the state machine) and `evidence/` (append-only). Governed by [kernel/contract/work-state.md](../../../../kernel/contract/work-state.md) — read it before touching anything here.

The rules that matter most, restated because they're the contract's teeth:
- `body_hash` is mechanical and agent-written; only a human sets `status: approved`, `approved_by`, `approved_at`, and `approval_code` (except the chat-only floor rung, which requires its own `evidence/<yyyy-mm-dd>-approval.md` artifact).
- Agents recompute the body digest before executing; mismatch = stop and re-gate.
- Evidence is append-only. Scratch goes to `.folioos/.scratch/` (gitignored), never here.

Approving a plan by hand:
```sh
# hash everything below the frontmatter's closing ---
awk 'n>=2{print} /^---$/{n++}' plan.md | shasum -a 256
# then set status: approved, approved_by, approved_at, approval_code (first 8 hex) in the frontmatter
```
