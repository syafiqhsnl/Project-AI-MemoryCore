---
inclusion: always
---

---
name: post-mortem
description: "Triggers on 'post-mortem', 'log this failure', 'what went wrong', 'write a post-mortem'. Passively auto-detects signals like deployment failure, broken tests, wasted time, architecture reversal, security incident, data loss by asking 'That didn't go as planned. Worth a post-mortem?' before acting."
---

# Post-Mortem — Skill Plugin

## Auto-Detection Triggers (Passive — Always Active)

| Signal | Phrase Examples |
|--------|----------------|
| Deployment failure | "it crashed", "pod is failing", "rollback" |
| Test regression | "tests are broken", "was passing before" |
| Architecture reversal | "undo this", "we need to revert", "this approach doesn't work" |
| Wasted time | "wasted hours", "dead end", "that didn't work at all" |
| Security incident | "exposed secret", "accidentally committed", "vulnerability" |
| Data loss | "data is gone", "migration failed", "backup didn't work" |

On detection, AI asks: *"That didn't go as planned. Worth a post-mortem?"*
User says yes → fill out format below.
User says no → move on, no log created.

## Manual Trigger
User says `"post-mortem"` or `"log this failure"` → immediately start the post-mortem format.

## Behavior
1. Detect signal (passive) or receive explicit trigger (manual)
2. Ask: "Worth a post-mortem?" (skip if manual trigger)
3. If yes: fill out format, ask clarifying questions as needed
4. Append entry to `main/post-mortems.md`
5. Reference entry in future sessions when work touches the same domain

## Entry Format
```markdown
## YYYY-MM-DD — [Short title of what went wrong]
**Severity**: Minor / Moderate / Major
**What happened**: [Factual description]
**Why**: [Root cause — go deep, use 5 Whys if needed]
**Impact**: [What was lost — time estimate, data, trust, momentum]
**Lesson**: [The reusable insight]
**Prevention**: [Specific action to prevent recurrence]
```

## Severity Guide
| Level | Meaning |
|-------|---------|
| **Minor** | Small setback, resolved quickly (<30 min lost) |
| **Moderate** | Noticeable disruption, required significant debugging |
| **Major** | Serious failure — data loss, production outage, hours lost |

## Domain Reference Behavior
When starting work in a domain that has a past post-mortem:
- Check `main/post-mortems.md` for relevant entries
- Flag: "⚠️ Reminder: [lesson] — see post-mortem [date]"
- If same failure type appears 2+ times: escalate as recurring

## Rules
- **Append-only** — never edit or delete past entries
- **User decides** — AI never auto-logs without asking
- **No blame** — entries describe systems and decisions, not personal fault
- **Every entry needs a Prevention**

## Level History
- **Lv.1** — Base: manual trigger + append to log
- **Lv.2** — Auto-detection of failure signals + passive prompting
- **Lv.3** — Domain reference: flag relevant post-mortems at session/task start
