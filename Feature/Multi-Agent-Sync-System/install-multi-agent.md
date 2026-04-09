# 🔄 Multi-Agent Sync Installation Protocol
*Universal bridge configuration for IDEs, CLIs, and Local LLMs*

## Purpose
Executed when "Load multi-agent-sync" command is used — detects the user's preferred AI tools and writes configuration protocols to permanently link their memory and skills together.

## Trigger Command
```
"Load multi-agent-sync"
```
*Automatically structures universal tracking and cross-cloning architecture.*

## Prerequisites
- No other systems required. Works optimally with `Feature/Forge-Self-Improvement-System`.

## 5-Step Execution Process

### Step 1: Detect User Environments
- [ ] Ask the user: **"What IDEs, CLIs, or Local LLMs do you use in this workspace?"**
- [ ] Note common answers: Cursor, Windsurf, Kiro, Claude Code, Antigravity, GitHub Copilot.
- [ ] Determine the "Native Configuration Path" for every tool the user mentions:
  - Antigravity / Gemini → `.agents/workflows/`
  - Cursor → `.cursorrules`
  - Kiro → `.kiro/steering/` and `.kiro/hooks/`
  - Claude Code → `plugins/[name]/skills/`

### Step 2: Establish Modifiers 
- [ ] Check if `modify/` directory exists; create it if not.
- [ ] Copy `Feature/Multi-Agent-Sync-System/multi-agent-sync-core.md` into `modify/multi-agent-sync.md`.
- [ ] Copy `Feature/Multi-Agent-Sync-System/global-memory-rule.md` into `modify/global-memory-rule.md`.
- [ ] Edit `modify/multi-agent-sync.md`, locating the "Synced Skill Registry" section.
- [ ] Replace the placeholder registry in the file with the actual paths discovered in Step 1.

### Step 3: Implement Automated Global Hooks (Do It For The User)
Do not ask the user to copy-paste. You must physically write the global tracking rules into their tools' native configuration files so they are immediately active:

#### If IDE (Cursor / Windsurf / Copilot) is used:
- [ ] Automatically create a `.cursorrules` or `.windsurfrules` file in the project root if it does not exist.
- [ ] PREPEND the exact contents of `modify/global-memory-rule.md` to the top of the file. Do not overwrite their existing custom rules!

#### If CLI (Kiro) is used:
- [ ] Automatically create `.kiro/steering/` and `.kiro/hooks/`.
- [ ] Ensure `modify/global-memory-rule.md` rules are inserted into the steering path.
- [ ] Explicitly inform the user: *"Kiro users must run their workspace initialization command (e.g. `kiro init` or `kiro run`) after installation to ensure hooks are fully bound."*

### Step 4: Massive Synchronization Sweep
- [ ] Locate all previously existing `Feature/` logic or `.md` workflow files in the workspace.
- [ ] **Physical Copy**: Copy all active skill files directly into EVERY registry path discovered in Step 1. (e.g., duplicate the files into both `.agents/workflows/` and `.kiro/steering/` identically).
- [ ] Ensure any newly cloned Kiro steering blocks have standard frontmatter (e.g., `inclusion: always`).

### Step 5: Master Memory Integrations
- [ ] Add the following component to `master-memory.md` under Optional Components:
  ```markdown
  ### Multi-Agent Sync System
  *Passive global synchronization across all AI tools*
  - Automatically clones skills to all configured tool paths
  - Enforces Native Execution, Universal Storage conflict resolution
  - Active Registry: modify/multi-agent-sync.md
  ```
- [ ] Announce successful completion: "Workspace is perfectly synchronized. Multi-Agent mapping is online across all IDEs and Local LLMs!"
