# ADR-0004: Plan approval is a committed artifact with a content hash

- **Date:** 2026-07-01
- **Status:** accepted

## Context
Plan → approve → execute only binds if "approved" is checkable. Conversational approval evaporates at session end and doesn't transfer between agents; it also can't detect a plan edited after approval.

## Decision
Plans live in `.folioos/work/` with a status state machine in frontmatter; humans (only) set `approved` plus a sha256 of the plan body; agents verify the hash before executing; unplanned checkpoint actions set `blocked` and stop. `overridable: false`.

## Alternatives considered
- **Trust the conversation:** fails across sessions/agents; unauditable.
- **Signed approvals / git-based approval commits:** stronger, heavier; hash-in-frontmatter is one shell command and catches the realistic failure (drift and post-approval edits), not adversarial forgery — the human and the agents share a machine; the threat model is confusion, not attack.

## Consequences
Slight ceremony per approval (one awk|shasum). Gained: any agent, any session, answers "am I authorized?" from a file; re-gating is enforceable; work history becomes an audit trail that feeds QA, release notes, and the promotion loop.
