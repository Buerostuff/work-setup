# Software List

## Required Software

| Software | Description | Download |
|----------|-------------|----------|
| [PowerShell](#powershell) | Modern shell | [Microsoft Docs](https://learn.microsoft.com/en-us/powershell/scripting/install/install-powershell-on-windows) |
| [Starship](#starship) | Shell prompt | [starship.rs](https://starship.rs) |
| [Git](#git) | Version control | [git-scm.com](https://git-scm.com) |
| [Cursor](#cursor) | AI-powered code editor | [cursor.com](https://cursor.com) |
| [VS Code](#vs-code) | Code editor | [code.visualstudio.com](https://code.visualstudio.com) |
| [Docker](#docker) | Container platform | [docker.com](https://www.docker.com/products/docker-desktop) |
| [Node.js](#nodejs) | JavaScript runtime | [nodejs.org](https://nodejs.org) |
| [Python](#python) | Programming language | [python.org](https://www.python.org/downloads) |
| [Go](#go) | Programming language | [go.dev](https://go.dev/dl) |
| [Greenshot](#greenshot) | Screenshot tool | [getgreenshot.org](https://getgreenshot.org) |
| [Espanso](#espanso) | Text expander | [espanso.org](https://espanso.org) |
| [Typora](#typora) | Markdown editor | [typora.io](https://typora.io) |

---

## PowerShell

Modern cross-platform shell.

=== "Windows"

    ```powershell
    # winget (recommended)
    winget install Microsoft.PowerShell

    # MSI installer
    # Download from https://learn.microsoft.com/en-us/powershell/scripting/install/install-powershell-on-windows
    ```

    For silent MSI install:

    ```powershell
    msiexec.exe /package PowerShell-7.5.4-win-x64.msi /quiet ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL=1 ADD_FILE_CONTEXT_MENU_RUNPOWERSHELL=1 ENABLE_PSREMOTING=1 REGISTER_MANIFEST=1 USE_MU=1 ENABLE_MU=1 ADD_PATH=1
    ```

=== "macOS"

    ```bash
    brew install powershell
    ```

> **_NOTE:_** See [Microsoft Docs](https://learn.microsoft.com/en-us/powershell/scripting/install/install-powershell-on-windows) for all installation options including MSI, ZIP, and .NET Global tool.

---

## Starship

Cross-shell prompt with customization.

=== "Windows"

    ```powershell
    # winget
    winget install Starship.Starship

    # scoop
    scoop install starship
    ```

=== "macOS"

    ```bash
    brew install starship
    ```

> **_NOTE:_** See [Terminal Setup](terminal-setup.md) for shell configuration.

---

## Git

Version control system.

=== "Windows"

    ```powershell
    # winget
    winget install Git.Git

    # scoop
    scoop install git
    ```

=== "macOS"

    ```bash
    brew install git
    ```

> **_NOTE:_** See [Git SSH Setup](git-ssh-setup.md) for SSH configuration.

---

## Cursor

AI-powered code editor built on VS Code.

=== "Windows"

    ```powershell
    # winget
    winget install Cursor.Cursor

    # manual
    # Download from https://cursor.com
    ```

=== "macOS"

    ```bash
    brew install --cask cursor
    ```

---

## VS Code

Code editor by Microsoft.

=== "Windows"

    ```powershell
    # winget
    winget install Microsoft.VisualStudioCode

    # scoop
    scoop install vscode
    ```

=== "macOS"

    ```bash
    brew install --cask visual-studio-code
    ```

---

## Docker

Container platform for building and running applications.

=== "Windows"

    ```powershell
    # winget
    winget install Docker.DockerDesktop

    # scoop
    scoop install docker
    ```

=== "macOS"

    ```bash
    brew install --cask docker
    ```

> **_NOTE:_** See [Docker Setup](docker-setup.md) for custom install paths and VDI configuration.

---

## Node.js

JavaScript runtime.

=== "Windows"

    ```powershell
    # winget
    winget install OpenJS.NodeJS.LTS

    # scoop
    scoop install nodejs-lts
    ```

=== "macOS"

    ```bash
    brew install node
    ```

---

## Python

Programming language.

=== "Windows"

    ```powershell
    # winget
    winget install Python.Python.3.12

    # scoop
    scoop install python
    ```

=== "macOS"

    ```bash
    brew install python
    ```

---

## Go

Programming language by Google.

=== "Windows"

    ```powershell
    # winget
    winget install GoLang.Go

    # scoop
    scoop install go
    ```

=== "macOS"

    ```bash
    brew install go
    ```

---

## Greenshot

Screenshot tool for Windows.

=== "Windows"

    ```powershell
    # winget
    winget install Greenshot.Greenshot

    # scoop
    scoop install greenshot

    # manual
    # Download from https://getgreenshot.org
    ```

=== "macOS"

    Not available. Use built-in screenshot (Cmd+Shift+4) or [Shottr](https://shottr.cc).

---

## Espanso

Cross-platform text expander and snippet manager.

=== "Windows"

    ```powershell
    # winget
    winget install Espanso.Espanso

    # scoop
    scoop install espanso

    # manual
    # Download from https://espanso.org/install
    ```

=== "macOS"

    ```bash
    brew install espanso
    ```

=== "Linux"

    ```bash
    # Snap
    sudo snap install espanso --classic

    # Manual
    # See https://espanso.org/install
    ```

---

## Typora

Markdown editor with live preview.

=== "Windows"

    ```powershell
    # winget
    winget install Typora.Typora

    # scoop (requires extras bucket)
    scoop bucket add extras
    scoop install typora

    # manual
    # Download from https://typora.io
    ```

=== "macOS"

    ```bash
    brew install --cask typora
    ```

> **_NOTE:_** Typora requires a license (not free). See [typora.io](https://typora.io) for pricing.

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
