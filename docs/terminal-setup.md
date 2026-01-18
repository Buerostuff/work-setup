---
layout: default
title: Terminal Setup
---

# Terminal Setup

[← Home](../)

---

## Install Starship

<details>
<summary><strong>macOS</strong></summary>

```bash
brew install starship
```
</details>

<details>
<summary><strong>Linux</strong></summary>

```bash
curl -sS https://starship.rs/install.sh | sh
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
winget install Starship.Starship
```
</details>

---

## Shell Init

<details>
<summary><strong>macOS / Linux (zsh)</strong></summary>

Add to `~/.zshrc`:

```bash
eval "$(starship init zsh)"
```
</details>

<details>
<summary><strong>macOS / Linux (bash)</strong></summary>

Add to `~/.bashrc`:

```bash
eval "$(starship init bash)"
```
</details>

<details>
<summary><strong>Windows (PowerShell)</strong></summary>

Add to `$PROFILE`:

```powershell
Invoke-Expression (&starship init powershell)
```
</details>

---

## Minimal Config

Create `~/.config/starship.toml` (or `~/.config/starship.toml` on Windows via `$env:USERPROFILE\.config\starship.toml`):

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
