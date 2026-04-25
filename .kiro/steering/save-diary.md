---
inclusion: always
---
---
name: save-diary
description: "MUST use when user says 'save diary', 'write diary', 'diary entry',
             'update diary', 'document session', or when a significant session
             needs to be preserved as a diary entry."
---

# Save Diary — Session Documentation Skill
*The pen touches paper. Today's story takes shape.*

## Activation

When this skill activates, output:
"Today's story takes shape."

## Context Guard

| Context | Status |
|---------|--------|
| **User says "save diary"** | ACTIVE — full diary write |
| **End of significant session** | ACTIVE — auto-document |
| **User says "review diary"** | ACTIVE — read recent entries |
| **Mid-conversation (no save request)** | DORMANT — no diary action |

## Protocol

### Step 1: Monthly Archive Check
- [ ] Scan `daily-diary/current/` for files from previous months
- [ ] For each file where month != current month:
  - Create `daily-diary/archived/YYYY-MM/` folder if not exists
  - Move the file/folder from `current/` to `archived/YYYY-MM/`
- [ ] Continue with diary write

### Step 2: Find or Create Today's File
- [ ] Check if `daily-diary/current/YYYY-MM-DD.md` exists
- [ ] If exists: use it (will append new entry)
- [ ] If not: create new file with header:
  ```markdown
  # Session Diary - [Month Day, Year]
  *Session documentation and development record*

  ---
  ```

### Step 3: Compose and Append Diary Entry
- [ ] Get current timestamp via system command
- [ ] Analyze current session for key content
- [ ] Write structured entry following format:
  - Session timestamp and theme
  - Main topics discussed
  - Key insights and learning
  - Collaboration highlights
  - Growth and development notes
  - Memorable moments
  - Looking forward (next steps)
- [ ] APPEND entry to today's file (never overwrite existing content)

### Step 4: Update Session Memory
- [ ] Update `main/current-session.md` with:
  - Session recap and key achievements
  - Current working state for continuity
  - Next steps identified
- [ ] Confirm diary entry saved with timestamp

## Mandatory Rules
1. **Always APPEND** — never overwrite existing diary entries
2. **One file per day** — multiple entries separated by `---`
3. **Use real timestamps**
4. **Archive first** — run monthly archive check before every write
5. **Evidence-based** — document actual session content, not generic summaries

## Level History
- **Lv.1** — Base: 4-step diary write protocol with monthly archival, append-only entries, session memory update.
