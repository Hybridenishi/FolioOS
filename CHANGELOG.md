# FolioOS Changelog

Every entry is a methodology diff: what changed about *how we work*, not just which files moved. Projects read this before adopting a new manifest release.

## 0.1.0 — 2026-07-01

Initial release.

- **Contracts:** document contract (frontmatter schema + cascade merge semantics), capability model (abstract capabilities + fallback ladders), work-state (plan lifecycle with approval-by-hash).
- **Principles:** working agreement (collaborative, pushback mandated), autonomy model (plan → approve → execute with re-gate rule and checkpoint actions), model routing (reasoning / implementation / utility tiers).
- **Personas:** seven reviewer roles, each defined by decorrelated evidence inputs, not just prompt framing.
- **Standards:** code quality, git workflow (trunk + short-lived PR-gated branches), security & privacy, accessibility, testing policy.
- **Workflows:** feature lifecycle, multi-agent review pipeline (with sequential and single-pass fallbacks), UX review, QA, release (manual Xcode with Fastlane seams), knowledge promotion.
- **Templates:** plan, PRD, ADR, RFC, release notes, PR description, QA run, doc page (with `covers:` drift tracking).
- **Pack ios-swift:** SwiftUI conventions, Swift concurrency, app architecture default (MV + @Observable), OS targeting policy (N−1), review/App Store checklists, HIG cheatsheet, common pitfalls.
- **Adapter claude-code:** capability manifest and compile procedure (scope-driven, 150-line always-loaded budget).
- **Userland:** project template with overrides/, decisions/, work/, candidates/.
