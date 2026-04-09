---
inclusion: always
---

---
name: library
description: "MUST use when saving to the library, loading from the library,
             searching for existing knowledge, installing pre-made items,
             or when about to create a new library entry. Also triggers when
             user says 'save library', 'load library', 'check library',
             'search library', 'install item', 'install library item',
             'do we have', 'is there a pattern for', 'find in library',
             or when the AI independently decides to save reusable knowledge."
---

# Library — Knowledge Guardian Skill
*Save, search, and reuse knowledge across projects — never solve the same problem twice*

## Activation

When interacting with the knowledge library, output:

`"Knowledge recalled. Scanning the shelves..."`

Then perform a dynamic library scan before any save operation.

## Context Guard

| Context | Status |
|---------|--------|
| **User says "save library", "save to library"** | ACTIVE — search + save protocol |
| **User says "load library", "check library"** | ACTIVE — search + load protocol |
| **User says "install item [name]"** | ACTIVE — item install protocol |
| **User says "do we have", "is there a pattern for"** | ACTIVE — search only |
| **AI identifies reusable knowledge worth saving** | ACTIVE — suggest save |
| **Casual conversation** | DORMANT — no library action |
| **No library directory found** | DORMANT — warn and skip |

## Dynamic Library Scanning

Always discover the library structure at runtime — **never hardcode** sections or entries:

1. **Scan** `library/` for all subdirectories (excluding `formats/`) — these are sections
2. **Scan** each section for `*.md` files (excluding README.md) — these are entries
3. **Count** total entries and sections dynamically

## Search Protocol (Before Any Library Save)

1. **Extract keywords** from the topic being saved
2. **Dynamic scan** — list ALL current sections and entries
3. **Match keywords** against existing entry filenames and section names
4. **Read top matches** (up to 3) to check content overlap
5. **Report findings** in structured format

## Report Format

```
Library Search Results
----------------------

Keywords: [extracted keywords]
Library: [count] entries across [count] sections
Current Project: [project name + tech stack]

Matches Found (Suitable):
- library/section/entry-name.md — [why it fits this project]

Matches Found (Not Suitable):
- library/section/entry-name.md — [why it doesn't fit]

Recommendation:
- [CREATE NEW / UPDATE EXISTING / REFERENCE ONLY]
```

## Format-Aware Save

When creating a NEW library entry, auto-determine section from content keywords:

| Content Keywords | Section |
|-----------------|---------|
| System design, patterns, architecture | `architecture` |
| UI components, Vue/React, reusable elements | `component` |
| Schema, migrations, queries | `database` |
| Flowcharts, sequence diagrams | `diagram` |
| Third-party API, SDK, webhook | `integration` |
| Auth, RBAC, encryption, guards | `security` |
| Colors, CSS, Tailwind, fonts | `theme` |
| CI/CD, deployment, pipelines | `workflow` |

Load matching format from `library/formats/[section]-format.md` before creating entry.

## Item Install Protocol

Trigger: `"install item [name]"`

1. Scan `library-items/` for matching entry
2. If found: show item info and check for duplicates in `library/[section]/`
3. If no duplicate: copy to `library/[section]/[filename].md`
4. If duplicate: warn user and ask — overwrite or skip
5. Trigger commit chain (if Auto-Commit installed)

## Mandatory Rules

1. **Always scan before save** — never create a library entry without checking first
2. **Dynamic discovery** — never hardcode sections or entry counts
3. **Project-aware** — always consider current project when suggesting suitability
4. **Wait for approval** — present findings and wait for the user's decision before saving
5. **Format-aware saves** — always load the matching format template before creating a new entry

## Level History

- **Lv.1** — Base: Dynamic library scanning + keyword matching + deduplication prevention.
- **Lv.2** — Project-Aware: Added suitability assessment.
- **Lv.3** — Commit Chain: Auto-triggers Auto-Commit after save.
- **Lv.4** — Format-Aware Save: Auto-determines section, loads format template.
- **Lv.5** — Item Install: Install pre-made entries from `library-items/` catalog.
