# Multi-Agent Synchronization System
*A universal pipeline for synchronizing skills and memory across ANY AI Agent, IDE, or CLI.*

## Overview
This system prevents your MemoryCore from fracturing when you use multiple AI tools. Whether you are using a native IDE (like Cursor/Windsurf), a CLI-based agent (like Kiro/Claude Code), or a local LLM, this system guarantees that rules, workflows, and memory states remain instantly synchronized across all of them.

## The Philosophy

We adhere to the **Native Execution, Universal Storage** rule:
1. **Execution**: Your AI tool should use its most powerful *native* capabilities to solve a task. (e.g., if it has an advanced native planner, use that instead of a basic markdown checklist).
2. **Storage**: Once the execution is complete, the AI **must** save the final results back into standard, universal markdown files (such as `main/current-session.md` or `daily-diary/YYYY-MM-DD.md`).
3. **Synchronization**: All AI logic (workflows, skills, rules) is mirrored natively into every active tool's local configuration directory.

## Supporting "Any Tool"

This system is completely **platform-agnostic**. It does not matter what tool you use. All you need to do is map the directory where your specific tool reads its custom instructions into the `Synced Skill Registry` during installation.

**Examples of Tool Mapping:**
- **Antigravity / Gemini Shell**: Reads from `.agents/workflows/`
- **Cursor / Windsurf**: Reads from `.cursorrules` or `.windsurfrules`
- **Kiro**: Reads from `.kiro/steering/`
- **Claude Code**: Reads from `plugins/`
- **Local LLMs**: Reads from whatever arbitrary `custom_prompts/` folder you assign it.

The installer does the heavy lifting of cloning and updating files across all of these disparate locations.

## Contents
- **install-multi-agent.md**: The installer wizard that dynamically detects your tools and builds your unified registry.
- **multi-agent-sync-core.md**: The core engine that enforces the cloning synchronization rules.
- **global-memory-rule.md**: The system prompt injection that forces AI agents to adhere to the MemoryCore protocols globally.
