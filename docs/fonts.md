# Nerd Fonts

## JetBrains Mono NF

=== "macOS"

    ```bash
    brew install --cask font-jetbrains-mono-nerd-font
    ```

=== "Linux"

    ```bash
    mkdir -p ~/.local/share/fonts
    cd ~/.local/share/fonts
    curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
    unzip JetBrainsMono.zip -d JetBrainsMono
    rm JetBrainsMono.zip
    fc-cache -fv
    ```

=== "Windows"

    ```powershell
    # scoop
    scoop bucket add nerd-fonts
    scoop install JetBrainsMono-NF

    # manual
    Invoke-WebRequest -Uri "https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip" -OutFile "JetBrainsMono.zip"
    Expand-Archive JetBrainsMono.zip -DestinationPath JetBrainsMono
    # Right-click .ttf files → Install
    ```

---

## Set Font in Terminal

=== "macOS (iTerm2)"

    Preferences → Profiles → Text → Font → `JetBrainsMono Nerd Font`

=== "Linux"

    Terminal settings → Font → `JetBrainsMono Nerd Font`

=== "Windows Terminal"

    Settings → Profile → Appearance → Font face → `JetBrainsMono Nerd Font`

=== "VS Code"

    ```json
    {
      "terminal.integrated.fontFamily": "JetBrainsMono Nerd Font"
    }
    ```

---

## Test Icons

```bash
echo "     "
```

If you see boxes, font is not set correctly.
