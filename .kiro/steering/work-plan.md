---
inclusion: always
---

---
name: work-plan
description: "MUST use when user says 'copy plan', 'append plan', 'resume plan',
             'load plan', 'start the plan', 'continue the plan', 'execute plan',
             'run the plan', 'pick up where we left off', or when the AI exits
             plan mode and needs to transfer the plan into execution format. This
             skill manages the full lifecycle of project plans — from plan output
             to tracked checkbox execution with per-todo commits."
---

# Work — Plan Execution Skill
*Plan lifecycle management with tracked execution and context recovery*

## Activation

| Command | Activation Message |
|---------|-------------------|
| **Copy Plan** | `"Copying plan to execution format..."` |
| **Append Plan** | `"Appending to existing plan..."` |
| **Resume Plan** | `"Resuming plan execution..."` |

## Context Guard

| Context | Status |
|---------|--------|
| **User says "copy plan", "start the plan"** | ACTIVE — copy and begin execution |
| **User says "append plan"** | ACTIVE — append to existing plan |
| **User says "resume plan", "continue the plan"** | ACTIVE — resume from checkpoint |
| **AI exits plan mode with approved plan** | READY — suggest "copy plan" to user |
| **No project context** | DORMANT |

## Command Dispatch

| Command | What It Does |
|---------|-------------|
| `"copy plan"` | Copy latest plan to `projects/plans/project-plan.md` (fresh start) |
| `"append plan"` | Append latest plan to existing `project-plan.md` |
| `"resume plan"` | Resume execution after context reset (pick up from next `[ ]`) |

## Copy Plan

1. Scan `projects/plans/sources` for most recently modified plan file
2. Convert steps into `- [ ]` checkbox todo items
3. Preserve all architecture diagrams (ASCII, mermaid)
4. Write to `projects/plans/project-plan.md`
5. Begin Shared Execution Loop

## Append Plan

1. Find latest plan (same as Copy Plan step 1)
2. Check existing plan line count
3. If under 1000 lines: append with date separator `## Appended: YYYY-MM-DD`
4. If over 1000 lines: archive old as `project-plan-YYYYMMDD.md`, create fresh
5. Begin Shared Execution Loop

## Resume Plan

1. Read `projects/plans/project-plan.md`
2. Count `[x]` completed, `[ ]` pending, `[~]` blocked
3. Identify next `[ ]` as resumption point
4. Report status, then begin Shared Execution Loop

## Shared Execution Loop

```
For each [ ] todo item in order:
  1. Execute the task
  2. If Auto-Commit installed → trigger commit for this item
  3. Mark item as [x] in plan file
  4. Every 5 completed items → save plan file (checkpoint)
  5. Move to next [ ] item
  6. If item is [~] (blocked) → skip and continue
```

## Mandatory Rules

1. **Commit chain per-todo** — every completed todo triggers a commit (if Auto-Commit installed)
2. **Never commit plan files** — `project-plan*.md` stays local only
3. **Preserve diagrams** — carry over all visual elements from original plan
4. **No emoji in plan files** — clean, parseable markdown only
5. **Line limit 1000** — rotate on exceed (archive old, create fresh)
6. **Recovery-first design** — the plan file IS the recovery mechanism
7. **Skip blocked items** — mark `[~]`, flag to user, continue to next
8. **Checkpoint every 5 items** — update plan file mid-execution

## Level History

- **Lv.1** — Base: Three commands (copy/append/resume) + shared execution loop + per-todo commit chain + line rotation + recovery mechanism + checkpoint saves.
- **Lv.2** — Wave Execution: Dependency-aware wave grouping for parallel sub-agent execution.
