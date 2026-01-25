# Terminal Setup

## Install Prompt Tools

=== "Starship"

    ```powershell
    # winget
    winget install Starship.Starship

    # scoop
    scoop install starship
    ```

=== "Oh-My-Posh"

    ```powershell
    # winget
    winget install JanDeDobbeleer.OhMyPosh

    # scoop
    scoop install oh-my-posh
    ```

---

## Starship Configuration

### Apply Preset

```powershell
starship preset no-empty-icons -o $env:USERPROFILE/.config/starship.toml
```

### Custom Config

Edit `~/.config/starship.toml`:

```toml
add_newline = true

[character]
success_symbol = "[➜](green)"
error_symbol = "[✗](red)"

[directory]
truncation_length = 3

[git_branch]
symbol = " "
```

---

## Oh-My-Posh Configuration

Themes are built-in. Use theme name directly (e.g., `star`):

```powershell
oh-my-posh init pwsh --config star --eval | Invoke-Expression
```

Browse themes: [Oh-My-Posh Themes](https://ohmyposh.dev/docs/themes)

---

## Windows Terminal Profile

### Profile Command Line

=== "Starship"

    ```
    C:\Install\Programs\PowerShell\7\pwsh.exe -NoExit -ExecutionPolicy Bypass -Command "Set-PSReadlineKeyHandler -Key UpArrow -Function HistorySearchBackward; Set-PSReadlineKeyHandler -Key DownArrow -Function HistorySearchForward; Invoke-Expression (&starship init powershell)"
    ```

=== "Oh-My-Posh"

    ```
    C:\Install\Programs\PowerShell\7\pwsh.exe -NoExit -ExecutionPolicy Bypass -Command "Set-PSReadlineKeyHandler -Key UpArrow -Function HistorySearchBackward; Set-PSReadlineKeyHandler -Key DownArrow -Function HistorySearchForward; oh-my-posh init pwsh --config star --eval | Invoke-Expression"
    ```

### PSReadLine Keybindings (included above)

| Key | Function |
|-----|----------|
| `UpArrow` | Search history backward (type partial command, press up) |
| `DownArrow` | Search history forward |

---

## Windows Terminal settings.json

Open: Settings → Open JSON file (Ctrl+Shift+,)

### Profile Example (Starship)

```json
{
    "colorScheme": "Tango Dark",
    "commandline": "C:\\Install\\Programs\\PowerShell\\7\\pwsh.exe -NoExit -ExecutionPolicy Bypass -Command \"Set-PSReadlineKeyHandler -Key UpArrow -Function HistorySearchBackward; Set-PSReadlineKeyHandler -Key DownArrow -Function HistorySearchForward; Invoke-Expression (&starship init powershell)\"",
    "font": {
        "face": "MesloLGLDZ Nerd Font Mono",
        "size": 10,
        "weight": "semi-bold"
    },
    "guid": "{4d1dbe22-c281-47fd-9231-54b739c16e92}",
    "name": "pwsh 7",
    "opacity": 93,
    "startingDirectory": "C:\\Install\\_github",
    "tabTitle": "pwsh"
}
```

### Default Profile Settings

Add to `profiles.defaults` for all profiles:

```json
{
    "profiles": {
        "defaults": {
            "colorScheme": "Tango Dark",
            "font": {
                "face": "MesloLGL Nerd Font Mono",
                "size": 10,
                "weight": "medium"
            },
            "opacity": 95,
            "startingDirectory": "C:\\Install\\_github"
        }
    }
}
```

### Useful Global Settings

```json
{
    "centerOnLaunch": true,
    "copyFormatting": "none",
    "copyOnSelect": false,
    "theme": "dark",
    "useAcrylicInTabRow": true
}
```

### Keybindings

```json
{
    "keybindings": [
        { "id": "Terminal.CopyToClipboard", "keys": "ctrl+c" },
        { "id": "Terminal.PasteFromClipboard", "keys": "ctrl+v" },
        { "id": "Terminal.FindText", "keys": "ctrl+shift+f" },
        { "id": "Terminal.DuplicatePaneAuto", "keys": "alt+shift+d" }
    ]
}
```

---

## Quick Setup Checklist

1. ✅ Install PowerShell 7
2. ✅ Install Nerd Font (MesloLGLDZ or JetBrainsMono)
3. ✅ Install Starship or Oh-My-Posh
4. ✅ Create new profile in Windows Terminal
5. ✅ Set as default profile
6. ✅ Apply starship preset (optional)
