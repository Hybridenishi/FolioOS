# FolioOS Design: Architecture

The design rationale of record, as revised by the 2026-07 architecture review. The contracts themselves live in `kernel/contract/`; this explains *why the system is shaped this way*.

## Layering by rate of change

Kernel (rare) → Packs (platform cycle) → Adapters (vendor cycle) → Userland (constant). Layer boundaries follow rate-of-change because that's what makes a system maintainable for years: things that change together live together; things that change on different clocks are separated so one's churn doesn't destabilize the other.

## The contract stack

```
┌──────────────────────────────────────────────────────┐
│ USERLAND (per project)                               │
│  overrides · work/ (plans w/ approval-by-hash)       │
│  decisions/ · candidates/ ──── promotion loop ──┐    │
├──────────────────────────────────────────────── │ ───┤
│ ADAPTERS   claude-code · codex(stub) · gemini(stub)  │
│  capability manifest + compile procedure        │    │
│  compile(kernel ⊕ pack ⊕ overrides, caps)       │    │
│    → always-core (150-line budget)              │    │
│    → on-demand commands/agents (scope metadata) │    │
├──────────────────────────────────────────────── │ ───┤
│ PACKS      ios-swift (independent version)      │    │
├──────────────────────────────────────────────── ▼ ───┤
│ KERNEL     document contract ← the real product      │
│            capability model + fallback ladders       │
│            work-state (approval as artifact)         │
│            principles · standards · workflows ·      │
│            personas · templates · checklists         │
└──────────────────────────────────────────────────────┘
        MANIFEST.md pins the tested combination
```

## Key decisions (each has an ADR in `.folioos/decisions/`)

1. **Documents are abstracted by schema, capabilities by vocabulary.** The two hard interop problems, solved separately: *what a document is* (frontmatter contract) and *what an agent can do* (capability manifests + fallback ladders). Adapters become mechanical because both are specified.
2. **Approval is an artifact.** Plan state lives in committed frontmatter with a content hash — so authorization survives sessions, agents, and vendors, and post-approval tampering is detectable.
3. **Independent component versioning.** Kernel, packs, adapters version separately (different clocks); the manifest pins tested combinations; semver has a defined methodology meaning.
4. **Context economy is compile-time.** `scope:` metadata + a hard always-loaded budget, because instruction adherence degrades with always-loaded volume. `always` is a scarce resource by design.
5. **The OS learns, gated.** candidates → triage → human-gated promotion. Knowledge flows both directions; editorial control flows only through the human.
6. **Reviews are decorrelated by evidence, verified adversarially.** Personas consume different inputs; findings survive a refutation attempt before reaching the human. Multi-agent review earns its cost by decorrelation, not headcount.
7. **Files first.** Everything hand-operable; tooling is convenience. The lowest common denominator across all future agents is a file system — so that's the substrate.

## Known non-goals (v0.1)

- No CLI (procedures are manual by design until usage proves the automation worth its maintenance).
- No eval harness for methodology changes (changelog discipline + the DX-engineer feedback loop stand in; revisit if kernel changes start regressing outcomes).
- Codex/Gemini adapters are stubs (build against real tools when adopted, not imagined ones).

## The failure mode to guard against

FolioOS requiring more maintenance than the apps it governs. Guardrails: tiny schema, hard budgets, files-first, WWDC-cycle pack review as the only scheduled maintenance, and the DX-engineer persona explicitly watching for bloat and routinely-overridden standards.
