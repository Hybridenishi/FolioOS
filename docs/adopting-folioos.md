# Adopting FolioOS in a Project

Files-first promise: every step below is a hand-operable procedure. Future tooling (`folioos init/update/doctor`) may automate them, but must do exactly this — if the tooling ever rots, this document is still the system.

## New project

1. **Pin.** Copy `userland/project-template/.folioos/` into the project root. Fill in `pin.md` (manifest release + path to your FolioOS checkout — a sibling directory or a git submodule, your call; the contract only needs a readable path).
2. **Ignore scratch.** Append `gitignore-snippet` to the project's `.gitignore`.
3. **Compile.** Run your agent's adapter procedure (for Claude Code: [adapters/claude-code/compile.md](../adapters/claude-code/compile.md)). Commit the emitted artifacts (`CLAUDE.md`, `.claude/…`).
4. **ADR-0001.** Record the app's architecture (even if it's just "pack default").
5. **First plan.** All non-trivial work now starts as a plan in `.folioos/work/`.

## Existing project

Same steps; additionally have the agent do a one-time gap read — where the codebase already contradicts pack standards, record the *status quo* as ADRs or overrides rather than pretending. FolioOS governs changes going forward; it doesn't demand a big-bang rewrite.

## Updating a project's FolioOS version

1. Read `CHANGELOG.md` between the pinned and target manifest releases — it's written as a methodology diff for exactly this moment.
2. Decide per-change whether any project override needs adjusting.
3. Bump `pin.md`, recompile the adapter artifacts, commit both together (an updated pin with stale compiled artifacts is the lie the compile-stamp header exists to catch).

## Maintaining FolioOS itself

- Changes to FolioOS follow FolioOS: non-trivial methodology changes get a plan in FolioOS's own `.folioos/work/`; decisions get ADRs in its `.folioos/decisions/`.
- Version bumps: see the semver definition in [README.md](../README.md). Every bump gets a CHANGELOG entry and a MANIFEST row.
- Each WWDC cycle: pack review (knowledge re-verification, os-targeting update).
- Periodically: run knowledge-promotion triage across your projects' `candidates/`.
