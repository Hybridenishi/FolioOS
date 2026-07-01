# FolioOS

**An AI development operating system.** FolioOS is not an application — it is the versioned methodology that AI coding agents (Claude Code today; Codex, Gemini, and others tomorrow) use to build and maintain applications. It encodes how work is planned, approved, built, reviewed, tested, documented, and shipped.

## The four layers

| Layer | Analogy | Contents | Rate of change | Tool-specific |
|---|---|---|---|---|
| **Kernel** | kernel | Contracts, principles, personas, standards, workflows, templates, checklists | Rarely, deliberately | No |
| **Packs** | drivers | Platform knowledge (`packs/ios-swift/` today) | Per platform cycle | No |
| **Adapters** | syscall interface | Compilers from kernel+packs into per-agent artifacts | Per vendor | Yes |
| **Userland** | processes | Each governed app: overrides, decisions, work-state, candidates | Constantly | No |

Resolution cascade: **kernel → pack → project overrides**. Overrides are explicit deltas (`extend` / `replace` / `disable`), never forks. See [kernel/contract/document-contract.md](kernel/contract/document-contract.md).

## The three contracts (read these first)

1. **[Document contract](kernel/contract/document-contract.md)** — the frontmatter schema every kernel/pack document conforms to, and the cascade merge semantics. The kernel's real product is this schema; the markdown is content.
2. **[Capability model](kernel/contract/capability-model.md)** — workflows are written against abstract capabilities (`subagents`, `run-app`, `mcp:github`…) with declared fallback ladders, so they degrade gracefully on less-capable agents.
3. **[Work-state](kernel/contract/work-state.md)** — plans are committed artifacts with an explicit state machine (`draft → approved → executing → done`). Approval is recorded by content hash, so *any* agent in *any* session can answer "am I authorized to execute this?" by reading a file.

## Core principles

- **Plan → Approve → Execute.** Agents plan, stop, and wait for human approval before building. See [autonomy-model.md](kernel/principles/autonomy-model.md).
- **Pushback is a deliverable.** Agreement is not. See [working-agreement.md](kernel/principles/working-agreement.md).
- **Model routing.** Deep reasoning, implementation, and mechanical work go to different model tiers. See [model-routing.md](kernel/principles/model-routing.md).
- **Files first.** Everything FolioOS does must be doable by hand with a file system and the documented procedures. Tooling is convenience, never a dependency.
- **The OS learns.** Project discoveries flow back up via the [knowledge promotion loop](kernel/workflows/knowledge-promotion.md).

## Versioning

Kernel, each pack, and each adapter are versioned independently (`VERSION` file in each). [MANIFEST.md](MANIFEST.md) pins the tested combination; projects pin the manifest. Semver meaning for a methodology:

- **major** — contract changes (document schema, autonomy model, work-state)
- **minor** — new or changed standards/workflows/checklists
- **patch** — clarifications and wording

Every change lands in [CHANGELOG.md](CHANGELOG.md) as a human-readable methodology diff.

## Adopting FolioOS in a project

See [docs/adopting-folioos.md](docs/adopting-folioos.md). Short version: copy `userland/project-template/.folioos/` into your repo, pin the manifest version, run the adapter compile procedure for your agent.

## Repository map

```
kernel/          contract/ principles/ personas/ standards/ workflows/ templates/ checklists/
packs/ios-swift/ pack.md standards/ checklists/ knowledge/
adapters/        claude-code/ codex/ gemini/
userland/        project-template/
docs/            adopting-folioos.md design/
.folioos/        FolioOS's own decision log (we dogfood)
```
