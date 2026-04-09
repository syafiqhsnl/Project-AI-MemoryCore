---
inclusion: always
---

---
name: session-briefing
description: "Fires automatically at the start of every new conversation session, before processing the user's first message at session start. Or when user says 'brief', 'session brief', 'what did we do last time', 'where did we leave off'. Suppress with 'skip brief'."
---

# Session Briefing — Skill Plugin

## Activation Condition
Fires automatically at the start of every new conversation session, before processing the user's first message.

## Step-by-Step Execution

### Step 1: Load Context
Read in parallel:

| File | Purpose | Required |
|------|---------|----------|
| `main/current-session.md` | Last session recap | Yes |
| `main/reminders.md` | Open reminder items | Optional |
| `projects/project-list.md` | Active project + idle days | Optional |
| Current time | Time period classification | Optional |

### Step 2: Classify Time Period

| Time | Period | Work Suggestion |
|------|--------|----------------|
| 06:00–11:59 | Morning | Architecture, new features, complex problems |
| 12:00–17:59 | Afternoon | Implementation, debugging, testing |
| 18:00–21:59 | Evening | Code review, docs, moderate tasks |
| 22:00–05:59 | Night | Light tasks, planning — suggest wrapping up |

### Step 3: Identify Attention Flags

| Flag | Meaning | Threshold |
|------|---------|-----------|
| 🟡 | Idle — approaching warning | 14 days |
| 🔴 | Stale — past critical threshold | 30 days |

Show max 3 flags, most critical first. Skip if none.

### Step 4: Compose Brief

```
📋 Session Brief · [Time Period]

Last session: [1–2 line recap from current-session.md]
Active: [project name] · [status]
🔴/🟡 [project name] — [N] days idle
Reminders: [N] open → [first item preview]
Suggestion: [time-appropriate work type]
```

Composition rules:
- Total lines: 12 maximum
- Omit any line/section that has nothing to report
- "Last session" is the only mandatory line
- Deliver before processing the user's first request

## Level History
- **Lv.1** — Base: session recap + time suggestion
- **Lv.2** — Reminders integration
- **Lv.3** — Project health flags
