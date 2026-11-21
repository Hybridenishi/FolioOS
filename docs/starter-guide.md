# FolioOS Starter Guide & Prompt Cookbook

A plain-language field guide for working with FolioOS (Manifest `0.7.0`, work-state `v2`). Paste any prompt below directly into Claude Code, T3 Code, or any file-capable AI agent.

---

## How FolioOS works day to day (30-second version)

```
Ask for a plan → Read the approval card → Approve (or revise) → Agent builds & checks → Review report → You merge
```

- **Trivial changes** (typos, single-file mechanical edits with no behavior risk, doc refreshes, questions) happen directly — no plan needed.
- **Everything else** starts as a **draft plan** in `.folioos/work/<yyyy-mm>-<slug>/plan.md`. The agent stops and prints an **approval card** with an 8-character `approval_code`.
- **Nothing builds until you approve.** If the plan changes after you approve it, the code no longer matches and the agent must stop and ask again.
- **High-risk actions always wait for you:** schema/persistence changes, deletions beyond the plan, new dependencies, pushing branches, opening PRs, and releases never happen silently.

---

## Copy-paste starter prompts

### 1. Adopt FolioOS in a new or existing project

Use this once when bringing a repository onto FolioOS.

```text
Adopt FolioOS (Manifest 0.7.0) in this repository using the FolioOS checkout at <path/to/FolioOS>.

Follow <path/to/FolioOS>/docs/adopting-folioos.md:
1. Inspect the codebase, CI/test scripts, existing docs, and git authorship history first (existing-project gap read).
2. Copy userland/project-template/.folioos/ into .folioos/, update .folioos/pin.md (note the active pack, or "none", and orchestrator slice if using T3 Code), and append gitignore-snippet to .gitignore.
3. Record .folioos/decisions/0001-<slug>.md for our current architecture (plus any status-quo overrides rather than rewriting existing code).
4. If multiple providers/models are available, run <path/to/FolioOS>/kernel/workflows/routing-discovery.md to generate .folioos/overrides/model-routing.md and a commit-authorship ADR.
5. Compile CLAUDE.md (strictly <= 150 lines) and .claude/ per <path/to/FolioOS>/adapters/claude-code/compile.md.

Stop before making any source-code changes, pushing, or opening a PR, and summarize what you found and configured in plain language.
```

---

### 2. Set up or refresh multi-provider model routing

Run this when adopting T3 Code, changing available providers/models, adding a major subsystem, or noticing repeated misrouting on a type of task.

```text
Run <path/to/FolioOS>/kernel/workflows/routing-discovery.md for this project using the FolioOS checkout at <path/to/FolioOS>.

1. Check .folioos/overrides/ first — if an override already targets kernel/principles/model-routing, update it in place rather than creating a duplicate target.
2. Inspect our modules, verification/CI loops, high-risk invariant seams (Vendor-Split triggers), modality needs, parallel-safe seams vs. sequential shared-state bottlenecks (both machine/device state and shared canonical data/generated files), and any past failure evidence in .folioos/ or docs/.
3. Query active providers/models (for T3 Code, call orchestrator_capabilities once; tag uncalibrated live placements as "provisional").
4. Tailor the project's routing override, map review personas to decorrelated providers, align our commit-authorship ADR, and recompile CLAUDE.md (<= 150 lines, pointer + guardrails only), /plan, /folio-review, .claude/agents/*.md, and .folioos/pin.md.
5. Validate the document-contract checklist and summarize our routing table and sequential guardrails in plain language. Do not push or open a PR.
```

---

### 3. Ask for a plan (without letting the agent start coding)

If you compiled `/plan`, you can also just run `/plan <what you want>`.

```text
Write a FolioOS draft plan in .folioos/work/<yyyy-mm>-<slug>/plan.md for:
<describe the goal or bug in plain language>

Before drafting, read the relevant files and check our tests/standards.
Follow kernel/templates/plan.md (v3):
- Start with a short plain-language summary (what changes, options, cost/risk, what "yes" vs. "no" does).
- Tag each step with Model (<tier or work shape> @ <effort>), Size (S | M | L), Depends on, and Routing notes (Vendor-Split, parallel-safe, sequential shared-state, vision required).
- Name every checkpoint action (schema/data format changes, deletions, new dependencies, pushes/PRs) explicitly.
- Keep status: draft, compute body_hash with the normative awk command, and end with the approval card. Stop and wait for my approval.
```

---

### 4. Approve — or ask for changes on — a plan

Every draft plan ends with an approval card like:

```text
Plan:          .folioos/work/<slug>/plan.md
Body hash:     sha256:<full>
Approval code: <8 hex>
Approve by:    status: approved · approved_by: <you> · approved_at: <yyyy-mm-dd>
               · approval_code: <8 hex>
Or reply:      approve <slug> <8 hex>
```

Pick whichever approval path fits where you're reviewing:

- **From chat / mobile (floor rung):** Reply verbatim:
  ```text
  approve <slug> <8 hex>
  ```
  *(The agent records the frontmatter and saves your verbatim message + timestamp to `evidence/<yyyy-mm-dd>-approval.md`.)*
- **From an editor (fallback rung):** Open `.folioos/work/<slug>/plan.md` and set `status: approved`, `approved_by: <your-name>`, `approved_at: <yyyy-mm-dd>`, and `approval_code: <8 hex>`.
- **From a terminal (preferred rung):** Verify the digest independently first, then update the frontmatter:
  ```sh
  awk 'n>=2{print} /^---$/{n++}' .folioos/work/<slug>/plan.md | shasum -a 256
  ```
- **Need changes before approving?** Don't approve — ask the agent to revise the plan. Any edit to the plan body voids prior approvals and generates a fresh approval card with a new 8-character code.

---

### 5. Execute an approved plan

```text
Execute approved plan .folioos/work/<yyyy-mm>-<slug>/plan.md.

1. Recompute the plan body digest with awk and verify approval_code matches before editing anything.
2. Create branch feat|fix|chore/<slug> matching the plan directory.
3. Follow our routing rules: call orchestrator_capabilities once before delegating, pass complete 4-part self-contained briefs to delegated workers, parallelize only along declared parallel-safe seams, keep sequential shared-state seams on one owner, and apply Two-Strike escalation rather than retrying a third time.
4. Run the mandatory Lead verification pass on any delegated work (inspect git diff --stat and actual test/lint output).
5. Run the plan's Test plan, update status: done only if all checks pass, and stop before pushing or opening a PR. Report exactly what passed, failed, or was skipped.
```

---

### 6. Run a review (everyday vs. high-risk checkpoint)

**Everyday review** (after building a branch; if `/folio-review` is compiled, you can just run `/folio-review`):

```text
Run our FolioOS review pipeline on .folioos/work/<yyyy-mm>-<slug>/ (diff against main).

Select only the personas that fit this project and change, capture any shared-state evidence (tests, screenshots, live checks) sequentially first, then dispatch evidence-isolated persona reviews across decorrelated providers (applying Vendor-Split on code review). Run adversarial verification on findings, write one synthesis report to evidence/<yyyy-mm>-review.md opening with a plain-language summary, and stop at the human gate before fixing anything or merging.
```

**High-risk checkpoint review** (add this for persistence schemas, migrations, published-ID contracts, sync, security boundaries, or releases):

```text
This change touches a high-risk invariant seam, so run a formal <path/to/FolioOS>/kernel/workflows/cross-vendor-review.md pass in addition to standard review.

Build a self-contained, unanchored review packet under .folioos/.scratch/packet-<slug>/ (excluding our earlier review findings and rationale), dispatch an isolated zero-history cross-vendor review on a different model family than the author, file the raw output verbatim to evidence/<yyyy-mm-dd>-crossvendor-<vendor>.md, and highlight every cross-vendor delta for my decision.
```

---

### 7. Update a project to a newer FolioOS release

```text
Update this project's FolioOS pin to Manifest <target-version> using <path/to/FolioOS>.

1. Read <path/to/FolioOS>/CHANGELOG.md between our current pin in .folioos/pin.md and <target-version>, and summarize what changed in plain language.
2. Check whether any .folioos/overrides/ need updating, and re-run routing-discovery.md if model-routing, providers, or subsystems changed.
3. Update .folioos/pin.md, recompile CLAUDE.md (<= 150 lines) and .claude/ artifacts, verify the plan approval card and kernel version stamp, and stop before committing/pushing.
```

---

### 8. Record a model failure or durable lesson

Use this whenever an AI model trips over a task type or you discover a project rule worth keeping.

```text
Record what just went wrong on <task or step> as a FolioOS learning:
1. Write a dated candidate note in .folioos/candidates/<yyyy-mm-dd>-<slug>.md describing what failed, why, and the evidence file/commit.
2. If a model repeatedly underperformed on this work shape, demote or exclude that (model, effort) pair for that shape in our routing override (linking the evidence path) and recompile our instructions. Real-work failure outranks probe passes.
```

---

### 9. Sanity-check a new reviewer or model (`R3` / calibration)

Run this before trusting a brand-new or `provisional` model as sole cross-vendor reviewer on high-risk work.

```text
Calibrate <provider/model> at effort <low|medium|high|xhigh> using <path/to/FolioOS>/kernel/workflows/model-calibration.md.

If we only need to qualify it as a cross-vendor reviewer first, run 3 independent passes of kernel/calibration/reasoning-03-adversarial-verify.md at that exact effort level, have a stronger distinct model grade binary pass/fail against kernel/calibration/rubric.md (with me spot-checking), and record the dated result including the exact effort level.
```

---

## Quick reference

| When you want to… | Use | Source |
|---|---|---|
| Adopt FolioOS in a repo | Prompt #1 | [adopting-folioos.md](adopting-folioos.md) |
| Map or refresh multi-provider routing | Prompt #2 | [routing-discovery.md](../kernel/workflows/routing-discovery.md) |
| Start non-trivial work safely | `/plan` or Prompt #3 | [plan.md](../kernel/templates/plan.md) |
| Approve a plan from anywhere | `approve <slug> <code>` | [work-state.md](../kernel/contract/work-state.md) |
| Build an approved plan | Prompt #5 | [model-routing.md](../kernel/principles/model-routing.md) |
| Review built work | `/folio-review` or Prompt #6 | [review-pipeline.md](../kernel/workflows/review-pipeline.md) |
| Add a second opinion on risky changes | Prompt #6 (checkpoint) | [cross-vendor-review.md](../kernel/workflows/cross-vendor-review.md) |
| Upgrade FolioOS version in a repo | Prompt #7 | [CHANGELOG.md](../CHANGELOG.md) |
| Demote a flaky model or save a lesson | Prompt #8 | [knowledge-promotion.md](../kernel/workflows/knowledge-promotion.md) |
| Qualify a new model at an effort level | Prompt #9 | [model-calibration.md](../kernel/workflows/model-calibration.md) |
