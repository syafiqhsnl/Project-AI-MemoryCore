---
inclusion: always
---

---
name: observation
description: "MUST use when user says 'survey project', 'scan project', 'check health',
             'investigate', 'deep dive', 'what's going on in', 'look into',
             'refine code', 'clean up code', 'review changes', 'sharpen',
             'audit system', 'full audit', 'show me everything',
             or when the AI needs to assess project health before planning,
             review code quality after implementation, or investigate a bug.
             Also triggers on 'how does this project look', 'what's the status',
             'review what I changed', 'check for issues'."
---

# Observation System — Tiered Code Awareness
*"See clearly before you act. Act only on what you see."*

## Activation

| Command | Activation Message | Time |
|---------|-------------------|------|
| **Survey** | `"Scanning project from above..."` | ~30 sec |
| **Investigate** | `"Focusing on [target]..."` | ~5 min |
| **Refine** | `"Reviewing changed code..."` | ~5 min |
| **Audit** | `"Revealing all connections..."` | ~15 min |

## Context Guard

| Context | Status |
|---------|--------|
| **User says "survey", "scan", "check health"** | ACTIVE — run Survey |
| **User says "investigate", "deep dive", "look into"** | ACTIVE — run Investigate |
| **User says "refine", "clean up", "review changes"** | ACTIVE — run Refine |
| **User says "audit", "full audit"** | ACTIVE — run Audit |
| **Before planning a large feature** | ACTIVE — Survey first |
| **After implementation, before commit** | ACTIVE — Refine |
| **Bug report or error investigation** | ACTIVE — Investigate (bug mode) |
| **Casual conversation** | DORMANT |

## Depth Levels

| Depth | Command | Question It Answers | Time |
|-------|---------|-------------------|------|
| **Lv.1** | Survey | "What's the state of the project?" | ~30 sec |
| **Lv.2** | Investigate | "What's happening in this specific area?" | ~5 min |
| **Lv.2** | Refine | "What can be improved in changed code?" | ~5 min |
| **Lv.3** | Audit | "Show me everything about this system." | ~15 min |

## Lv.1: Survey

Quick bird's-eye view — structure, tech stack, health, recent activity.

Output format:
```
Survey: [PROJECT NAME]

Structure: [X] backend | [Y] frontend | [Z] tests
Stack:     [framework] + [DB]
Health:    [Build status] | [Git status] | [Last commit]
Recent:    [Last 3-5 commits]
Lessons:   [N relevant past incidents] (if any)

Deeper analysis? → investigate <area>
Full audit?      → audit
```

## Lv.2: Investigate

Focused investigation — trace bugs, reveal hidden patterns, analyze code flow.

Modes: File Analysis | Topic Investigation | Bug Investigation | Code Review

Output format:
```
Investigate: [target]

Location:  [file path or topic scope]
Files:     [N files touching this area]

Findings:
  1. [Finding with file:line reference]

Hidden Patterns:
  [Non-obvious behavior]

Escalate: investigate <deeper area> | audit (if systemic)
```

## Lv.2: Refine (Corrective)

Reviews changed code for quality, reuse, efficiency — then fixes with permission.

Scope: `git diff` (no arg) | specific file | area keyword

Checklist: dead code, duplication, naming, complexity, N+1 queries, project rules compliance.

**Rule**: Always present findings first, apply fixes only after user approval.

Output format:
```
Refine: [scope]

Reviewed: [N] files, [N] lines changed

Found:
  [N] issues to fix
  [N] suggestions
  [N] files — clean

Details:
  [file:line] — [category] — [description]
```

## Lv.3: Audit

Full system audit — ALL connections, dependencies, data flows, risk assessment.

Produces: Architecture Map, Dependency Map, Data Flow Audit, Risk Assessment, Recommendations.

## Mandatory Rules

1. **Always dependency scan first** — never review code without understanding the actual stack
2. **Never assume standard library behavior** — read actual source
3. **Understand intent before judging** — don't "fix" what isn't broken
4. **Refine always asks permission before fixing**
5. **Compact output for Survey** — should fit on one screen

## Level History
- **Lv.1** — Base: Survey/Investigate/Refine/Audit four-tier system.
