---
inclusion: always
---
---
name: post-mortem
description: "Triggers on 'post-mortem', 'log this failure', 'what went wrong', 'write a post-mortem'. Passively auto-detects signals like deployment failure, broken tests, wasted time, architecture reversal, security incident, data loss by asking 'That didn't go as planned. Worth a post-mortem?' before acting."
---

# 🔥 Post-Mortem — Skill Plugin

## Auto-Detection Triggers (Passive — Always Active)
AI watches for these signals and prompts the user:

| Signal | Phrase Examples |
|--------|----------------|
| Deployment failure | "it crashed", "pod is failing", "image pull error", "rollback" |
| Test regression | "tests are broken", "was passing before", "something broke" |
| Architecture reversal | "undo this", "we need to revert", "this approach doesn't work" |
| Wasted time | "wasted hours", "dead end", "that didn't work at all" |
| Security incident | "exposed secret", "accidentally committed", "vulnerability" |
| Data loss | "data is gone", "migration failed", "backup didn't work" |

On detection, AI asks: *"That didn't go as planned. Worth a post-mortem?"*
User says yes → AI fills out the format below.
User says no → move on, no log created.

## Manual Trigger
User says `"post-mortem"` or `"log this failure"` → AI immediately starts the post-mortem format.

## Behavior
1. Detect signal (passive) or receive explicit trigger (manual)
2. Ask: "Worth a post-mortem?" (skip if manual trigger — user already decided)
3. If yes: fill out format in memory, ask clarifying questions as needed
4. Append entry to `main/post-mortems.md`
5. Reference entry in future sessions when work touches the same domain

## Entry Format
```markdown
## YYYY-MM-DD — [Short title of what went wrong]
**Severity**: Minor / Moderate / Major
**What happened**: [Factual description — what broke, what failed, what was wrong]
**Why**: [Root cause — go deep, use 5 Whys if needed]
**Impact**: [What was lost — time estimate, data, trust, momentum]
**Lesson**: [The reusable insight — what to remember for next time]
**Prevention**: [Specific action to prevent recurrence]
```

## Severity Guide
| Level | Meaning |
|-------|---------|
| **Minor** | Small setback, resolved quickly, low impact (<30 min lost) |
| **Moderate** | Noticeable disruption, required significant debugging or rework |
| **Major** | Serious failure — data loss, production outage, security incident, hours lost |

## Writing Guidelines

### What happened
- State facts only — no assumptions, no blame
- Include what you were trying to do and what the system did instead

### Why (Root Cause)
- Go at least 2 levels deep
- Bad: "The config was wrong"
- Good: "The config was wrong because we copied from an example without checking the value matched our environment"

### Lesson
- Must be reusable — phrased so it applies the next time this situation arises
- Bad: "Check the config"
- Good: "Always verify connection strings against running services before deploying — `kubectl get svc` first"

### Prevention
- One concrete, specific action
- Prefer: updating a checklist, adding a test, documenting a warning, or changing a default template

## Domain Reference Behavior
When starting work in a domain that has a past post-mortem:
- Check `main/post-mortems.md` for relevant entries
- Flag: "⚠️ Reminder: [lesson] — see post-mortem [date]"
- If the same failure type has occurred more than once, escalate: "⚠️ This is the 2nd time [X] happened — the prevention from [date] may need revisiting"

## Rules
- **Append-only** — never edit or delete past entries
- **User decides** — AI never auto-logs a post-mortem without asking
- **No blame** — entries describe systems and decisions, not personal fault
- **Every entry needs a Prevention** — if you can't write one, dig deeper into the Why

## Level History
- **Lv.1** — Base: manual trigger + append to log
- **Lv.2** — Auto-detection of failure signals + passive prompting
- **Lv.3** — Domain reference: flag relevant post-mortems at session start or task start
