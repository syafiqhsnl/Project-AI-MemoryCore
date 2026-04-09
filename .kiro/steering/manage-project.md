---
inclusion: always
---

---
name: manage-project
description: "Auto-triggers on 'new project [name]', 'load project [name]',
             'save project', 'list projects'. Also triggers on 'create project',
             'resume project', 'open project', 'show projects', or when user
             mentions switching between active projects."
---

# Manage Project -- LRU Project Management Skill
*Smart project tracking with automatic memory management.*

## Activation

When this skill activates, silently determine which command was triggered and execute the matching protocol section.

## Context Guard

| Context | Status |
|---------|--------|
| **User says "new project [name]"** | ACTIVE -- create project |
| **User says "load project [name]"** | ACTIVE -- load and resume |
| **User says "save project"** | ACTIVE -- save current progress |
| **User says "list projects"** | ACTIVE -- display all projects |
| **Mid-conversation (no project command)** | DORMANT |

## Protocol: New Project

1. Extract project name, check for duplicates
2. Ask for brief description + tech stack
3. Create file from template at `projects/active/[name].md`
4. Insert at LRU position #1, shift others down, archive #11 if exists
5. Regenerate `projects/project-list.md`
6. Update `main/current-session.md` with active project

## Protocol: Load Project

1. Search `projects/active/` then `projects/archived/` (fuzzy match)
2. Move to position #1, shift others, archive #11 if exists
3. Update "Last Accessed" in project file
4. Regenerate `projects/project-list.md`
5. Load context into `main/current-session.md`

## Protocol: Save Project

1. Identify active project from `main/current-session.md`
2. Gather session progress (changes, decisions, milestones)
3. Parse `Time:` from Auto-Commit messages for duration
4. Update project file: Last Accessed, Duration, Session History
5. Enforce 1000-line limit (summarize old sessions if exceeded)
6. Regenerate `projects/project-list.md`

## Protocol: List Projects

1. Load `projects/project-list.md`
2. Display active (positions 1-10) and archived projects

## LRU Rules

- **Fixed Capacity**: 10 active projects
- **Auto-Archiving**: Position #11 gets archived automatically
- **Reactivation**: Archived projects can be loaded back

## Duration Display Thresholds

| Total Minutes | Format |
|---------------|--------|
| 1-59 | X min |
| 60-1439 | X hours |
| 1440-43199 | X days |
| 43200+ | X months |

## Embedded Project Template

```markdown
# [Project Name] - [Brief Description]

## Project Overview
- **Type**: [Category]
- **Period**: [Start Date] - Active
- **Tech Stack**: [Stack]
- **Completion**: 0%
- **Duration**: 0 min

## Current Status
- **Last Session**: [Date] - Project created
- **Next Steps**: [Initial goals]
- **Known Issues**: None

## Session History (Last 5)

### [Date] - Project Created
- **Changes**: Initial project setup
- **Time Spent**: ~0 min

## Historical Summary
[Populated when session count exceeds 5]

## Technical Notes
- **Repository**: [Git URL or local path]
- **Key Dependencies**: [Critical packages]

---
**Last Updated**: [Date] | **Position**: #1/10 Active
```

## Mandatory Rules

1. **10 active slots** -- auto-archive at position #11
2. **project-list.md regenerated** after every LRU operation
3. **Line limit 1000** -- auto-summarize when exceeded, keep last 5 sessions
4. **"save project" is project-only** -- does NOT save AI memory
5. **Explicit save only** -- no auto-save

## Level History
- **Lv.1** -- Base: new/load/save/list commands, LRU engine (10 slots), auto-archiving, session history, project-list.md auto-generation.
- **Lv.2** -- Duration Tracking + Line Limit Enforcement.
