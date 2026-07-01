---
id: kernel/templates/release-notes
type: template
layer: kernel
scope: on-release
requires: []
overridable: true
version: 1
---

# Template: Release Notes

Two audiences, one file. Drafted at utility tier from the merged work directories since the last tag; human edits the user-facing half for voice.

```markdown
# <App> <version> — <yyyy-mm-dd>

## For users            <!-- App Store "What's New" — benefits, not implementation -->
<Plain language. Lead with the most user-valuable change. "You can now…"
not "Refactored the…". Bug fixes summarized honestly: "Fixed an issue where…">

## For the record       <!-- internal; committed, not shipped to the store -->
- **Features:** <work-dir links>
- **Fixes:** <work-dir links, each with its regression test>
- **Decisions:** <ADRs recorded this cycle>
- **Known issues:** <shipped-with, deliberately>
- **Deployment target:** <e.g. iOS 26.0> · **Built with:** <Xcode/SDK version>
```
