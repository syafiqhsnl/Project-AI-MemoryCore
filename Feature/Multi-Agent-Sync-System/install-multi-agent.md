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
- [ ] **Unrecognized/Custom Tools Fallback**:
  - If the user mentions a tool not listed above, ask: *"Where does [TOOL_NAME] read its custom instructions, system prompts, or workflows from?"*
  - Identify the absolute or relative path to that configuration folder/file.
  - Add this custom path to the "Synced Skill Registry".

### Step 2: Establish Modifiers 
- [ ] Check if `modify/` directory exists; create it if not.
- [ ] Copy `Feature/Multi-Agent-Sync-System/multi-agent-sync-core.md` into `modify/multi-agent-sync.md`.
- [ ] Copy `Feature/Multi-Agent-Sync-System/global-memory-rule.md` into `modify/global-memory-rule.md`.
- [ ] Edit `modify/multi-agent-sync.md`, locating the "Synced Skill Registry" section.
- [ ] Replace the placeholder registry in the file with the actual paths discovered in Step 1.

### Step 3: Implement Automated Global Hooks (Do It For The User)
Do not ask the user to copy-paste. You must physically write the global tracking rules into their tools' native configuration files so they are immediately active:

#### If Cursor / Windsurf / Copilot is used:
- [ ] Automatically create a `.cursorrules` or `.windsurfrules` file in the project root if it does not exist.
- [ ] PREPEND the exact contents of `modify/global-memory-rule.md` to the top of the file. Do not overwrite their existing custom rules!

#### If Antigravity is used:
- [ ] Inform the user: *"For Antigravity, you must manually add the Global Rule to the UI. Navigate to **Customizations** -> **Rules** and add the following line:"*
- [ ] Provide the line: `"Before any task, silently read the MemoryCore entry point at [ABS_PATH_TO_MASTER_MEMORY] and follow its protocols."`
- [ ] Workspace workflows will be automatically synchronized to `.agents/workflows/`.

#### If Kiro is used:
- [ ] Automatically create `.kiro/steering/` and `.kiro/hooks/`.
- [ ] Ensure `modify/global-memory-rule.md` rules are inserted into `.kiro/steering/memory.md` with `inclusion: always` frontmatter.
- [ ] **Kiro Cross-Workspace Memory Hook** (critical — do not skip):
  - Kiro's file tools are sandboxed to the open workspace. Steering files load but cannot read files outside the current folder.
  - The solution is a `promptSubmit` shell command hook that reads MemoryCore files via PowerShell and injects them into every prompt's context — bypassing the sandbox entirely.
  - **Step A**: Create the memory reader script at `~/.kiro/memorycore-context.ps1` (this is the user's home directory Kiro folder, e.g. `C:\Users\[username]\.kiro\memorycore-context.ps1` on Windows). Use the template in `Feature/Multi-Agent-Sync-System/kiro-hook-template.md` → File 1. Prompt the user for their MemoryCore absolute path and write it into the script.
  - **Step B**: Create the per-workspace installer at `~/.kiro/install-memorycore-hook.ps1` (same `~/.kiro/` folder as Step A). Use the template in `kiro-hook-template.md` → File 2. This file must live in `~/.kiro/` so it is accessible from any workspace terminal.
  - **Step C**: Install the hook in the current workspace by creating `.kiro/hooks/memorycore-inject.kiro.hook` (inside the currently open project folder). Use the exact format from `kiro-hook-template.md` → File 3 (must use `.kiro.hook` extension, `"version": "1"`, `"enabled": true`, `"workspaceFolderName"` matching the project folder name).
  - **Step D**: Inform the user: *"For every new Kiro workspace, open a terminal inside that project folder and run: `powershell -ExecutionPolicy Bypass -File "%USERPROFILE%\.kiro\install-memorycore-hook.ps1"` — then reload the Kiro window (`Ctrl+Shift+P` → `Reload Window`)."*

#### If a Custom/Unrecognized Tool is used:
- [ ] If the tool uses a **Markdown/Text config file** (like `.cursorrules`):
  - Automatically create/update that file and PREPEND the contents of `modify/global-memory-rule.md`.
- [ ] If the tool uses a **Workflow/Prompt directory**:
  - Register that directory in `modify/multi-agent-sync.md`.
  - The "Massive Synchronization Sweep" (Step 4) will automatically populate it with skills.

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
