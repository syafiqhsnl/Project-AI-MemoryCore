---
inclusion: always
---

---
name: check-reminders
description: "Auto-triggers at session start to review open reminders. Also triggers
             on 'remind me', 'check reminders', 'don't forget', 'follow up on',
             'next session we should', or when user mentions a deadline."
---

# Check Reminders -- Persistent Follow-Up Skill
*Nothing gets lost between sessions.*

## Activation

When this skill activates, silently read `main/reminders.md`.

- If urgent/overdue items exist: mention them naturally
- If no urgent items at session start: stay silent
- If user is adding a reminder: confirm after saving

## Context Guard

| Context | Status |
|---------|--------|
| **Session start** | ACTIVE -- read and flag urgent items |
| **User says "remind me [X]"** | ACTIVE -- add reminder |
| **User says "check reminders"** | ACTIVE -- list all open reminders |
| **Task completed that matches a reminder** | ACTIVE -- move to Completed |
| **Session end** | ACTIVE -- review and update |
| **Mid-conversation (no reminder context)** | DORMANT |

## Protocol

### On Session Start
- Read `main/reminders.md`
- Flag items due within 3 days or overdue
- If urgent: weave into greeting naturally
- If none: stay silent

### On "Remind Me" / Adding
- Compose: `- **Title**: Description`
- With deadline: `- **Title** (by YYYY-MM-DD): Description`
- APPEND to `## Open` section in `main/reminders.md`
- Confirm: "Added reminder: [title]"

### On Task Completion
- Move matching item from `## Open` to `## Completed`
- Format: `- **Title** (completed YYYY-MM-DD): Outcome`

### On Session End
- Review Open items against session work
- Move resolved items to Completed with date
- Add any new follow-ups discovered

## Mandatory Rules
1. **Never delete reminders** -- always move to Completed section
2. **Absolute dates only** -- convert relative dates to YYYY-MM-DD
3. **Append-only for Open** -- never rewrite, only append or move
4. **Silent when empty** -- don't announce "you have no reminders"
5. **Natural integration** -- weave reminders into conversation naturally

## Level History
- **Lv.1** -- Base: session start/end lifecycle, natural language detection, deadline tracking, append-only Open section, move-to-Completed pattern.
