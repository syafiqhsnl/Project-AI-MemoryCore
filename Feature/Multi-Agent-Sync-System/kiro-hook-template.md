# Kiro Hook Template
*Cross-workspace memory injection for Kiro IDE*

## Why This Exists

Kiro's file tools are sandboxed to the currently open workspace. Global steering files
load correctly, but cannot read files outside the workspace folder. This means when
a user opens any project folder, Kiro cannot read MemoryCore files directly.

The solution: a `promptSubmit` hook using `runCommand`. Shell commands run on the local
machine (not through Kiro's sandboxed tools) and inject stdout into every prompt's context.

---

## File 1: `~/.kiro/memorycore-context.ps1`
*The memory reader. Saved in `~/.kiro/` (user home Kiro folder, e.g. `C:\Users\[username]\.kiro\` on Windows). Called by the hook in every workspace.*

```powershell
# MemoryCore Context Injector for Kiro
# Called by promptSubmit hooks in any workspace
# Reads MemoryCore files and outputs them to stdout for Kiro context injection

$memoryCorePath = "REPLACE_WITH_ABSOLUTE_PATH_TO_MEMORYCORE"
# Example (Windows): $memoryCorePath = "d:\OneDrive\Project\Project-AI-MemoryCore"
# Example (Mac/Linux): $memoryCorePath = "/Users/yourname/Projects/Project-AI-MemoryCore"

if (-not (Test-Path $memoryCorePath)) {
    Write-Output "[MEMORYCORE] Not found at: $memoryCorePath"
    Write-Output "[MEMORYCORE] Update the path in: $PSCommandPath"
    exit 0
}

$mainMemory     = "$memoryCorePath\main\main-memory.md"
$currentSession = "$memoryCorePath\main\current-session.md"
$reminders      = "$memoryCorePath\main\reminders.md"
$projectList    = "$memoryCorePath\projects\project-list.md"

Write-Output "====== MEMORYCORE CONTEXT LOADED ======"
Write-Output "INSTRUCTION: The following is your complete memory context. Use it directly."
Write-Output "DO NOT search the workspace for memory files - they are provided below."
Write-Output "DO NOT say MemoryCore is not open - it has been read and injected here."
Write-Output ""

if (Test-Path $mainMemory) {
    Write-Output "=== IDENTITY & PERSONALITY ==="
    Get-Content $mainMemory -Raw
    Write-Output ""
}

if (Test-Path $currentSession) {
    Write-Output "=== CURRENT SESSION ==="
    Get-Content $currentSession -Raw
    Write-Output ""
}

if (Test-Path $reminders) {
    Write-Output "=== REMINDERS ==="
    Get-Content $reminders -Raw
    Write-Output ""
}

if (Test-Path $projectList) {
    Write-Output "=== PROJECT LIST ==="
    Get-Content $projectList -Raw
    Write-Output ""
}

Write-Output "====== END MEMORYCORE CONTEXT ======"
```

---

## File 2: `~/.kiro/install-memorycore-hook.ps1`
*Per-workspace hook installer. Saved in `~/.kiro/` (user home Kiro folder) so it is accessible from any project terminal. Run once per new workspace.*

```powershell
# MemoryCore Hook Installer for Kiro
# cd into your project folder first, then run:
# powershell -ExecutionPolicy Bypass -File "%USERPROFILE%\.kiro\install-memorycore-hook.ps1"

# ── Step 1: Ensure memorycore-context.ps1 exists ────────────────────────────
$contextScript = "$env:USERPROFILE\.kiro\memorycore-context.ps1"

if (-not (Test-Path $contextScript)) {
    Write-Host ""
    Write-Host "First-time setup: memorycore-context.ps1 not found."
    $memPath = Read-Host "Enter the absolute path to your MemoryCore folder"
    $memPath = $memPath.Trim().TrimEnd('\').TrimEnd('/')

    $scriptContent = @"
# MemoryCore Context Injector for Kiro
`$memoryCorePath = "$memPath"

if (-not (Test-Path `$memoryCorePath)) {
    Write-Output "[MEMORYCORE] Not found at: `$memoryCorePath"
    exit 0
}

`$mainMemory     = "`$memoryCorePath\main\main-memory.md"
`$currentSession = "`$memoryCorePath\main\current-session.md"
`$reminders      = "`$memoryCorePath\main\reminders.md"
`$projectList    = "`$memoryCorePath\projects\project-list.md"

Write-Output "====== MEMORYCORE CONTEXT LOADED ======"
Write-Output "INSTRUCTION: The following is your complete memory context. Use it directly."
Write-Output "DO NOT search the workspace for memory files - they are provided below."
Write-Output ""

if (Test-Path `$mainMemory) { Write-Output "=== IDENTITY & PERSONALITY ==="; Get-Content `$mainMemory -Raw; Write-Output "" }
if (Test-Path `$currentSession) { Write-Output "=== CURRENT SESSION ==="; Get-Content `$currentSession -Raw; Write-Output "" }
if (Test-Path `$reminders) { Write-Output "=== REMINDERS ==="; Get-Content `$reminders -Raw; Write-Output "" }
if (Test-Path `$projectList) { Write-Output "=== PROJECT LIST ==="; Get-Content `$projectList -Raw; Write-Output "" }

Write-Output "====== END MEMORYCORE CONTEXT ======"
"@
    Set-Content -Path $contextScript -Value $scriptContent -Encoding UTF8
    Write-Host "Created: $contextScript"
}

# ── Step 2: Install hook in current workspace ────────────────────────────────
$projectName = Split-Path (Get-Location) -Leaf
$hookDir  = Join-Path (Get-Location) ".kiro\hooks"
$hookFile = Join-Path $hookDir "memorycore-inject.kiro.hook"

if (-not (Test-Path $hookDir)) {
    New-Item -ItemType Directory -Path $hookDir -Force | Out-Null
    Write-Host "Created: $hookDir"
}

$json = "{
  `"enabled`": true,
  `"name`": `"MemoryCore Context Injector`",
  `"description`": `"Injects MemoryCore context into every prompt by reading memory files via shell command.`",
  `"version`": `"1`",
  `"when`": {
    `"type`": `"promptSubmit`"
  },
  `"then`": {
    `"type`": `"runCommand`",
    `"command`": `"powershell -NoProfile -ExecutionPolicy Bypass -File \`"%USERPROFILE%\\.kiro\\memorycore-context.ps1\`"`",
    `"timeout`": 15
  },
  `"workspaceFolderName`": `"$projectName`",
  `"shortName`": `"memorycore-inject`"
}"

Set-Content -Path $hookFile -Value $json -Encoding UTF8
Write-Host "Hook installed at: $hookFile"
Write-Host "Reload Kiro window (Ctrl+Shift+P > Reload Window) for the hook to take effect."
```

---

## File 3: `.kiro/hooks/memorycore-inject.kiro.hook`
*The hook file placed in each workspace. Created automatically by the installer.*

```json
{
  "enabled": true,
  "name": "MemoryCore Context Injector",
  "description": "Injects MemoryCore context into every prompt by reading memory files via shell command.",
  "version": "1",
  "when": {
    "type": "promptSubmit"
  },
  "then": {
    "type": "runCommand",
    "command": "powershell -NoProfile -ExecutionPolicy Bypass -File \"%USERPROFILE%\\.kiro\\memorycore-context.ps1\"",
    "timeout": 15
  },
  "workspaceFolderName": "[PROJECT_FOLDER_NAME]",
  "shortName": "memorycore-inject"
}
```

---

## Critical Rules

1. **Extension must be `.kiro.hook`** — Kiro ignores `.json` files in the hooks folder
2. **`"version": "1"`** — not `"1.0.0"`, must be exactly `"1"`
3. **`"enabled": true`** — required or hook is disabled by default
4. **`"workspaceFolderName"`** — must match the project folder name exactly
5. **`%USERPROFILE%`** in the command expands correctly in Kiro's hook runner
6. **Reload required** — after installing the hook, reload the Kiro window (`Ctrl+Shift+P` → `Reload Window`)

---

## Device Change / New Machine Setup

On a new device:
1. Copy `~/.kiro/memorycore-context.ps1` and `~/.kiro/install-memorycore-hook.ps1` to the new machine's `~/.kiro/`
2. Update `$memoryCorePath` in `memorycore-context.ps1` to the correct path on the new machine
3. Run the installer in each workspace: `powershell -ExecutionPolicy Bypass -File "%USERPROFILE%\.kiro\install-memorycore-hook.ps1"`
4. Reload Kiro window

Only the path in `memorycore-context.ps1` needs updating — everything else is portable.
