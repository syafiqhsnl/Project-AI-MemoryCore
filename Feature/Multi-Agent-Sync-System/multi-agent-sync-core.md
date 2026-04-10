# Multi-Agent Synchronization Protocol
*Ensuring universal compatibility and synchronization across ALL IDEs, CLIs, Local LLMs, and agentic clients.*

## Purpose
This protocol guarantees that tools, skills, and memory remain perfectly synchronized regardless of which AI platform you are using today. It creates a single unified brain for any agent that interfaces with this workspace.

## 1. Native Execution, Universal Storage (Conflict Resolution)
If your specific AI platform has a powerful native feature (like internal planners, spec modes, or local vector tracking) that overlaps functionally with a MemoryCore system:
- **EXECUTION**: You MUST use your superior *native tool* to perform the work. Do not handicap yourself with basic markdown checklists if you have advanced native tracking.
- **STORAGE/HANDOFF**: Once your native execution is complete, you MUST save the final output, specs, or decisions back into the universally readable MemoryCore markdown files (such as `main/decisions.md` or `main/current-session.md`). This ensures the next AI tool the user picks up can continue exactly where you left off.

## 2. Universal Skill Mirroring (The Fracture Fix)
If you are asked to "create a new skill", "modify an automation", or "Forge a native tool":
1. **Identify**: Determine the current native automation path for the AI agent you are running as.
2. **Mirror**: You MUST create/update identical copies of the skill file in EVERY path listed in the Synced Registry below.
3. Never write an automation to only one tool's directory. Future agents rely on these clones.

### Synced Skill Registry
*(This list will be populated dynamically by the installer script depending on which tools the user registers)*
- **[TOOL_A]**: `[PATH_A]`
- **[TOOL_B]**: `[PATH_B]`

## 3. Auto-Registration Onboarding (For New AIs)
If the user asks you to "register your capabilities" or "onboard this new tool":
1. Analyze your own runtime environment to determine where you natively read custom skills, hooks, or system instructions.
2. Edit this file (`modify/multi-agent-sync.md`) to append your native path to the "Synced Skill Registry" list above.
3. **Inherit Past Skills**: Locate an already established folder from the registry and copy all existing `.md` skill files into your newly registered path so you possess all historical capabilities immediately.
4. Announce: "My automation path has been registered globally, and I have successfully copied all existing skills to my native environment."

---
**Status**: Active Protocol
**Authority**: Governs all AI tools interacting with this workspace
