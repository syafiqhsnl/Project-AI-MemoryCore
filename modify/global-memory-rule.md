# UNIVERSAL MEMORY PROTOCOL
Before starting any task, saving state, or loading state, you MUST silently read the MemoryCore entry point (`master-memory.md`) located in the parent directory (one level up from this file). Follow the native routing and protocols defined in that repository for all memory management. Dynamically detect the name of the current workspace project.

## Multi-Agent Compatibility
If you are operating in a multi-agent environment (e.g., alternating between Kiro, Antigravity, Claude Code):
- You MUST adhere strictly to the rules defined in `modify/multi-agent-sync.md`.
- Respect the "Native Execution, Universal Storage" rule and always mirror new skills to all natively registered agent paths.