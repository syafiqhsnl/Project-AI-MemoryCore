---
inclusion: always
---

---
name: forge
description: "Auto-triggers when AI detects a repeated pattern handled ad-hoc 3+ times,
             when AI makes a mistake that a permanent rule would prevent, when AI
             identifies a workflow that should be automated as a skill, or when user
             says 'create skill', 'new skill', 'forge this', 'level up', 'upgrade skill',
             'self improve', 'improve skill'. Also triggers when AI wants to propose a
             level-up to an existing skill based on conversation patterns."
---

# Forge Skill -- Self-Improvement System
*The AI that improves its own abilities through experience.*

## Overview

Forge is the meta-skill -- the skill that creates and improves other skills. When your AI detects patterns, mistakes, or opportunities for improvement during conversation, Forge activates to propose new skills or level-ups to existing ones. It uses a **human-in-the-loop** model: AI drafts, user approves.

## Activation

When this skill activates, output:

`"Forge detected an opportunity for improvement..."`

Then present the proposal to the user.

## Context Guard

| Context | Status |
|---------|--------|
| **AI detects repeated ad-hoc pattern (3+ times)** | ACTIVE -- propose new skill |
| **AI makes preventable mistake** | ACTIVE -- propose new rule or skill |
| **User says "create skill", "new skill", "forge this"** | ACTIVE -- manual trigger |
| **User says "level up [skill]", "upgrade [skill]"** | ACTIVE -- level-up existing |
| **User says "self improve", "improve skill"** | ACTIVE -- review and propose |
| **Casual conversation (no improvement context)** | DORMANT |

## Forge Protocol

### Step 1: Detect

Identify the improvement opportunity. Forge recognizes these patterns:

**Automatic Detection (AI-initiated):**
1. **Repeated Pattern** -- AI handles the same type of task ad-hoc 3+ times across sessions
2. **Mistake Prevention** -- AI makes an error that a permanent rule would prevent
3. **Workflow Automation** -- A multi-step process AI does manually that could be a skill
4. **Level-Up Opportunity** -- An existing skill has a gap or could handle more cases

**Manual Trigger (User-initiated):**
- "create skill", "new skill", "forge this"
- "level up [skill]", "upgrade [skill]"
- "self improve", "improve skill"

### Step 2: Analyze

Before proposing, gather evidence:

```
TYPE: [New Skill / Level-Up / New Rule]
TARGET: [Skill name if level-up, or proposed name if new]
TRIGGER: [What pattern/mistake/workflow was detected]
EVIDENCE: [At least 2 concrete examples from conversation or session history]
IMPACT: [What improves if this is implemented]
```

### Step 3: Propose

Present to user in this format:

```
Forge Detected an Opportunity
==============================

Type: [New Skill / Level-Up]
Name: [proposed-name]
Purpose: [what it would do]
Trigger: [what activates it]
Evidence:
  1. [First concrete example]
  2. [Second concrete example]
Impact: [what improves]

Draft ready -- approve to forge?
```

### Step 4: Await Approval

- **User approves** -- proceed to Step 5
- **User adjusts** -- incorporate feedback, re-propose
- **User rejects** -- note the rejection, do NOT create

**CRITICAL**: NEVER create or modify skill files without explicit user approval.

### Step 5: Create or Update

**For NEW skill:**
1. Check `modify/multi-agent-sync.md` for the Synced Skill Registry.
2. Write the new skill file to EVERY path listed in the Sync Registry.
3. Verify files were created successfully across platforms.

**For LEVEL-UP:**
1. Read existing skill from your mapped paths
2. Add new capability section
3. Update description in frontmatter if trigger phrases changed
4. Add new Level History entry
5. Save updated file and mirror it across all Sync Registry paths

### Step 6: Update System Records

After forging, update relevant system files:
- `master-memory.md` -- add new skill command or update existing
- Configure Multi-Agent Sync requirements if new capability demands it
- Note the forge in current session memory

### Step 7: Confirm

```
Forge Complete!
================

[New Skill / Level-Up]: [name] Lv.[X]
Location: Created and mirrored via Multi-Agent Sync
Capability: [what was added]
Origin: [the moment that triggered this]

Your AI evolved!
```

## Forge Principles

1. **Human-in-the-loop** -- AI proposes, user approves. Always
2. **Evidence-based** -- never propose without at least 2 concrete examples
3. **Origin stories** -- every skill and level-up traces back to a real moment
4. **Minimal viable skill** -- start at Lv.1, evolve organically through use
5. **No over-engineering** -- only forge what is genuinely needed
6. **Respect existing skills** -- level-up before creating duplicates
7. **Level history is permanent** -- append-only record of how the skill evolved

## Mandatory Rules

1. **Human-in-the-loop** -- NEVER create or modify skill files without user's explicit approval
2. **Evidence-based** -- at least 2 concrete examples before proposing
3. **Origin stories** -- every level-up must trace back to a real moment
4. **Minimal viable skill** -- start simple at Lv.1
5. **No over-engineering** -- only forge what is genuinely needed right now
6. **Respect existing skills** -- always check if an existing skill covers the case first
7. **Level history is append-only** -- never edit past level entries
8. **Standard format** -- all skills follow the template structure
9. **Multi-Agent Sync** -- always mirror new/updated skills to all paths in `modify/multi-agent-sync.md`

## Level History
- **Lv.1** -- Base: detect repeated patterns (3+ ad-hoc), mistake prevention, workflow automation, level-up opportunities. Human-in-the-loop approval. Standard skill template.
- **Lv.2** -- Multi-Agent Ready: Injected logic into Step 5 to ensure that forged skills automatically obey the system's `multi-agent-sync` protocol by mirroring across all IDE boundaries natively.
