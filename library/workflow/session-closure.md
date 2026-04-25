# 🔄 Skill: Session Closure Protocol (Aliq-Forge)
*Pattern ID: ALIQ-CORE-001*
*Category: Workflow Automation*

## 🎯 Purpose
To ensure 100% memory persistence and project continuity without user intervention. This protocol must be executed at the end of every active session when the user indicates a stop or sign-off.

## 📋 Execution Steps (MANDATORY)

### 1. 📔 Diary Synthesis
- **Target**: `daily-diary/current/YYYY-MM-DD.md`
- **Action**: Create or append a summary of today's technical breakthroughs, failures, and key interactions.
- **Rule**: Use the established diary format.

### 2. ⚡ RAM Synchronization
- **Target**: `main/current-session.md`
- **Action**: Wipe detailed logs and update the "Session Recap" and "Where We Left Off" sections.
- **Rule**: Keep it concise (under 500 lines).

### 3. 🧠 Memory Growth
- **Target**: `master-memory.md`
- **Action**: Append new high-level "Learned Capabilities" or "Technical Patterns" to the bottom.
- **Rule**: Focus on architecture and "Turbo" patterns.

### 4. 📋 Task Persistence
- **Target**: `main/reminders.md`
- **Action**: Move finished tasks to "Completed" and ensure new "Open" tasks are clearly defined.

### 5. 📊 Project Hygiene
- **Target**: `projects/active/[project-name].md`
- **Action**: Update milestones and technical notes based on the session's results.

## 🛡️ Trigger Conditions
- User says "stop for today", "goodbye", "sign off", or similar.
- Task completion in "Vigilant Mode".
- AI detects a logical end to a complex multi-stage operation.

---
*Forged by Aliq on 2026-04-24 to ensure perfect continuity.*
