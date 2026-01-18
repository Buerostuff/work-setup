---
layout: default
title: Fonts
---

# Nerd Fonts

[← Home](../)

---

## JetBrains Mono NF

<details>
<summary><strong>macOS</strong></summary>

```bash
brew install --cask font-jetbrains-mono-nerd-font
```
</details>

<details>
<summary><strong>Linux</strong></summary>

```bash
mkdir -p ~/.local/share/fonts
cd ~/.local/share/fonts
curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip -d JetBrainsMono
rm JetBrainsMono.zip
fc-cache -fv
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
# Download, extract, right-click .ttf → Install
Invoke-WebRequest -Uri "https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip" -OutFile "JetBrainsMono.zip"
Expand-Archive JetBrainsMono.zip -DestinationPath JetBrainsMono
```
</details>

---

## Set Font in Terminal

<details>
<summary><strong>macOS (iTerm2)</strong></summary>

Preferences → Profiles → Text → Font → `JetBrainsMono Nerd Font`
</details>

<details>
<summary><strong>Linux</strong></summary>

Terminal settings → Font → `JetBrainsMono Nerd Font`
</details>

<details>
<summary><strong>Windows Terminal</strong></summary>

Settings → Profile → Appearance → Font face → `JetBrainsMono Nerd Font`
</details>

<details>
<summary><strong>VS Code</strong></summary>

```json
{
  "terminal.integrated.fontFamily": "JetBrainsMono Nerd Font"
}
```
</details>

---

## Test Icons

```bash
echo "     "
```

If you see boxes, font is not set correctly.
