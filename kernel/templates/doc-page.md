---
id: kernel/templates/doc-page
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Template: Documentation Page

FolioOS docs are **AI-maintained**: comprehensive, but regenerated rather than hand-tended, so they can't drift silently. The mechanism is the `covers:` field — every page declares the source files it describes. When a covered file changes after `generated:`, the page is stale by definition; the technical writer persona's pass turns staleness into a regeneration worklist, executed at utility tier.

Hand-written *why* lives in ADRs, not here. Doc pages describe *what is*.

```markdown
---
title: <Page title>
covers:                      # source files this page describes — the drift tripwire
  - Sources/Import/ImportService.swift
  - Sources/Import/BackupFormat.swift
generated: <yyyy-mm-dd>      # last regeneration date
brief: >                     # the regeneration instruction — precise enough
  Describe the import/backup subsystem: supported formats, the restore     # for a utility-tier model to
  state machine, failure handling. Audience: future maintainer.            # execute without guessing
---

# <Page title>

<Body: current-state description. No history ("previously this…"), no
promises ("will eventually…") — those belong in ADRs and RFCs.>
```

## Staleness check (manual today, tooling later)

For each page: any file in `covers:` modified after `generated:` → stale. Regenerate per `brief:`, update `generated:`. A page whose `covers:` files no longer exist is dead — delete it or re-scope it, don't let it haunt the tree.
