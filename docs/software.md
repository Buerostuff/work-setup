# Software List

## Required Software

| Software | Windows | macOS |
|----------|---------|-------|
| Git | winget / scoop | brew |
| Docker Desktop | winget / scoop | brew |
| Azure CLI | winget / scoop | brew |
| VS Code | winget / scoop | brew |
| Windows Terminal | winget | - |
| PowerShell 7 | winget | brew |
| Starship | winget / scoop | brew |
| JetBrains Mono NF | scoop | brew |
| Firefox | winget / scoop | brew |
| Python | winget / scoop | brew |
| Go | winget / scoop | brew |
| Node | winget / scoop | brew |
| Bun | scoop | brew |

---

## Windows Terminal + PowerShell Setup

When group policy blocks `profile.ps1` execution, configure starship directly in Windows Terminal settings.

### Terminal Settings

Open Windows Terminal → Settings → Profiles → PowerShell → Command line:

```
pwsh.exe -ExecutionPolicy Bypass -NoExit -Command "Invoke-Expression (&starship init powershell)"
```

Or for Windows PowerShell (not pwsh):

```
powershell.exe -ExecutionPolicy Bypass -NoExit -Command "Invoke-Expression (&starship init powershell)"
```

### settings.json

Open `settings.json` (Ctrl+Shift+,) and edit the PowerShell profile:

```json
{
    "profiles": {
        "list": [
            {
                "name": "PowerShell",
                "commandline": "pwsh.exe -ExecutionPolicy Bypass -NoExit -Command \"Invoke-Expression (&starship init powershell)\"",
                "font": {
                    "face": "JetBrainsMono Nerd Font"
                }
            }
        ]
    }
}
```

**Flags explained:**

| Flag | Purpose |
|------|---------|
| `-ExecutionPolicy Bypass` | Ignores group policy restriction |
| `-NoExit` | Keeps terminal open after command |
| `-Command "..."` | Runs starship init on startup |
