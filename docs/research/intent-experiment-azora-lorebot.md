# Experiment 2: an existing project that never wrote its intent down

**Subject:** `azora-lorebot` (Hybridenishi/azora-lorebot @ `6d52f78`) — a shelved Discord bot for
querying D&D campaign lore from an Obsidian vault. Last touched July 2025, a year before FolioOS
existed. Selected by the project owner specifically as a harder test than
[experiment 1](intent-prd-experiment-foundryvtt-mcp.md).
**Run:** 2026-09-05
**Status:** Complete. **This experiment revises experiment 1's headline conclusion.**

---

## Why this ran

Experiment 1 concluded that project intent "already exists, spread across documents with
incompatible lifecycles," making the gap a **consolidation** problem rather than an authoring one.
That was drawn from a single, unusually well-documented project written while its author was
actively designing FolioOS. The conclusion needed a subject that predates that influence.

`azora-lorebot` qualifies on the calendar: last commit July 2025, twelve months before FolioOS
0.1.0. Same author, same problem domain (D&D campaign tooling), no methodology contact.

---

## Result 1 — the model was wrong: there are three modes, not two

`azora-lorebot` is **well documented by volume** — a 216-line `README.md` and a 259-line
`ROADMAP.md`, 475 lines total. On the surface it looks like experiment 1's subject.

It is not. Every one of those 475 lines describes **what the bot does**: command lists, setup
steps, vault layout, usage examples, planned features by phase. None of it describes **what binds
the bot** — no constraints, no non-goals, no statement of what it must never get wrong.

Meanwhile the codebase contains a 362-line spoiler-control system (`utils/visibilityUtils.js`)
deciding what campaign information players may see, and a `/dm-lore` command whose own description
reads *"DM-only access to full lore information without visibility filtering."*

**The words `visibility`, `spoiler`, `dm-only` and `player-safe` appear zero times across both
documents.** The single highest-consequence rule in the project exists only as code.

So the intake situation is not binary. It is three:

| Where intent lives | Subject | Cost to capture | Who can do it |
|---|---|---|---|
| Written in prose, scattered | `foundryvtt-mcp` | Low — gather and cite | Agent, nearly unaided |
| **Embedded in code, absent from prose** | **`azora-lorebot`** | **Moderate — derive and confirm** | **Agent derives, human confirms** |
| Does not exist yet | any new project | High — elicit | Human only |

The middle mode was missing from `intent-md-project-intent.md` §5, which offers exactly two
onboarding paths. It is also the *cheapest surprise* of the three: the visibility rule above was
derived from source in roughly ninety seconds. Derivation is far closer to consolidation than to
elicitation, which means §5's two paths are mis-split — the real division is **"can it be read off
the artifact"** (prose or code, both agent-tractable) versus **"must it be asked"** (human-only).

---

## Result 2 — a near-incident, which the evidence base did not previously have

`intent-md-project-intent.md` §7 conceded that the whole argument was structural: *"no incident is
on record."* This experiment produces the closest thing yet, as a natural comparison between two
projects by the same author in the same domain.

**`foundryvtt-mcp` — the rule is written down:**

- `ROADMAP.md` Phase 5 states it and says why: *"a wrong hit point value is corrected in seconds;
  DM notes rendered visible to a player cannot be un-seen."*
- Visibility is **required**, and a missing or unrecognized profile is **rejected** — fails closed.
- Receipts name every user who can see the result, read back from the written document.
- Tests enforce it, including one proving hidden and absent are indistinguishable.

**`azora-lorebot` — the rule is not written down:**

- Default when a note has no visibility set is `'partial'` (`visibilityUtils.js:76`) — it exposes
  something. Fails **open**, not closed.
- Of nine content-serving commands, **two** apply filtering (`character.js`, and `ask-lore.js` via
  `searchFilesWithVisibility`). `lore-search.js`, `location.js`, `session.js` and
  `generate-lore.js` read vault content with no filtering at all.
- `/dm-lore` bypasses filtering by design, and **no permission gate exists in the repository** —
  `setDefaultMemberPermissions` and any DM-role check are absent from every command file, so as
  committed, the DM-only command is registered for every member of the server.

Same author, same domain, opposite defaults. The project that wrote the constraint down enforces it
in code and tests; the project that did not has a spoiler system most of its commands skip, and an
unguarded bypass command.

**Three caveats, stated plainly, because this finding is the kind that gets over-claimed:**

1. **This is correlation, not proven causation.** `foundryvtt-mcp` is the newer project by a year;
   its author had simply learned more. Writing the rule down may be a *symptom* of the greater care
   rather than its cause.
2. **The Discord finding is scoped to the repository.** Discord server administrators can restrict
   individual commands through Discord's own settings, outside any file here. What is established
   is that nothing in the code enforces it — not that the live bot was exploitable.
3. **Nobody was harmed.** The bot is shelved and its credentials are dead. This is a latent defect
   found by inspection, not an incident that cost anything.

Even discounted for all three, it is stronger evidence than the note previously had, and it is
evidence of exactly the predicted shape: **the constraint that was never written is the constraint
that drifted.**

---

## Result 3 — the same project's documents already disagree with each other

A minor observation that supports §6's derived-staleness design. The same fact — how far the
campaign has progressed — is recorded in three places and no two agree: `README.md`'s worked
example uses session 36, `ROADMAP.md` discusses session 52, and `visibilityUtils.js:6` hardcodes
`this.currentSession = 52` with the comment *"Update this as campaign progresses."*

That last one matters more than it looks: `sessionRevealThreshold` gates spoiler release against
`currentSession`, so a stale constant in code silently changes what players are allowed to see.
A fact that governs disclosure is maintained by hand, in a comment, in three locations.

---

## What this changes

- **§3 and experiment 1's conclusion both need revising.** "Intent already exists, just scattered"
  holds for one project and not for this one. The accurate statement is that intent is *recoverable
  from the artifact* in both cases — from prose in one, from code in the other — and that only a
  genuinely new project requires elicitation.
- **§5's two onboarding paths are mis-divided.** Split on *recoverable vs. must-be-asked*, not on
  *new vs. existing*. An existing project with thin prose is closer to the greenfield case in
  documentation and closer to the brownfield case in cost.
- **The `## Standing constraints` section gains its second, stronger justification.** In experiment
  1 it was missing from the template. Here, its absence from a real project coincides with the
  exact failure it exists to prevent.
- **A drafting rule, promoted from suggestion to recommendation:** derive constraints from code, not
  only from prose. Had this project's charter been drafted from its documents alone, the visibility
  rule — its most consequential — would not have appeared in it at all.
- **Unchanged:** §7's weight objection, and the §10 persona question. Neither is touched.

---

## What this does not settle

- **Still two projects, one author, one domain.** Both are D&D campaign tooling built by the same
  person. Nothing here shows the pattern holds for another author or another subject area;
  `safe-to-spend` is the only non-campaign project available and remains unexamined.
- **The elicitation path remains completely untested.** Both experiments ran against existing code.
  No new project has been through this.
- **Whether a written constraint would actually have prevented the drift**, per caveat 1. The
  honest reading is that written constraints make drift *detectable*, which is a weaker and more
  defensible claim than that they prevent it.

---

## Appendix — intent recovered from `azora-lorebot`

Recorded here rather than in the project, which its owner intends to retire. Carried forward, this
is a starting point for the successor project. **Unratified; every line is inferred**, and the
provenance column is the experiment's actual measurement.

| # | Recovered intent | Source |
|---|---|---|
| 1 | A Discord bot giving one D&D table conversational access to campaign lore held in an Obsidian vault. | prose |
| 2 | Built for one specific campaign ("Azora"); a private campaign management tool, not a product. | prose |
| 3 | AI runs locally by default (LMStudio) rather than through a hosted service. | prose |
| 4 | **Players must not be shown lore they have not yet earned.** Spoiler control is per-note, with levels, section markers, and a session threshold that releases content as the campaign advances. | **code only** |
| 5 | **DM access is a deliberate bypass of that filter**, and therefore must be restricted to the DM. | **code only, unenforced** |
| 6 | Generated content is previewed and approved by a human before it is posted; only the requesting user may approve; requests expire. | prose |
| 7 | The vault is the source of truth; the bot reads and reports, it does not author campaign canon unasked. | inferred from both, weakest item |

**Open questions — human-only, could not be recovered from anything:**

- Is filtering meant to apply to *every* player-facing command? Four currently skip it. Is that an
  oversight to fix, or were those commands intended as DM tools?
- Should the default for an unmarked note be "show a partial view" (current behaviour) or "show
  nothing"? This is the single highest-value question in the list.
- Is a bot that *writes* to the vault in scope, or is it permanently read-and-report?
- Do players interact with it directly, or only the DM?

**Measurement: 4 of 7 recovered from prose, 2 from code alone, 1 uncertain — and the 2 from code
are the highest-consequence items in the set.**
