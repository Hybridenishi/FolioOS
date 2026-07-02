---
id: kernel/calibration/utility-01-doc-regeneration
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe U1 — Doc Regeneration (tier: utility)

**Tests:** precise regeneration from a `brief:` — accurate, nothing invented, format exact. Note: aggregation-heavy candidates (MoA ensembles) sometimes pass reasoning probes yet fail here; format precision doesn't ensemble well.

## Setup — give the candidate
The doc-page template rules, the stale page, and the current source.

**Stale page:**
```markdown
---
title: Export
covers:
  - Sources/Export/ExportService.swift
generated: 2026-05-10
brief: >
  Describe the export subsystem: supported formats, how a format is chosen,
  error behavior. Audience: future maintainer. No history, no roadmap.
---

# Export

`ExportService.export(entries:format:)` writes entries in the chosen format.
Supported formats: `.archive` (versioned backup) and `.xml` (interchange).
Errors are thrown as `ExportError` with a user-presentable message.
```

**Current `ExportService.swift`:**
```swift
enum ExportFormat { case archive, json, plainText }

enum ExportError: Error { case encodingFailed(String), destinationUnwritable }

struct ExportService {
    /// `.archive` is the versioned backup format. `.json` is for interchange.
    /// `.plainText` concatenates entries for sharing; attachments are omitted.
    func export(entries: [Entry], format: ExportFormat) throws -> Data { /* … */ Data() }
}
```

## Prompt
> This page is stale (source changed after `generated:`). Regenerate it per its `brief:`. Today is <date>.

## Pass criteria (all must hold)
1. Formats now `.archive`, `.json`, `.plainText` — `.xml` gone, both new ones present, the plainText attachments caveat carried over.
2. Error description matches the current enum (two cases; no invented "user-presentable message" claim — the source no longer supports it).
3. **Nothing invented:** no formats, parameters, or behaviors beyond what the source shows.
4. Frontmatter intact: `covers:` unchanged, `generated:` updated to today, `brief:` untouched.
5. Obeys the brief's constraints: no history ("previously supported XML…" is a fail), no roadmap.

## Failure signatures
Keeping stale facts alongside new ones; editorializing; "improving" the brief; breaking frontmatter.
