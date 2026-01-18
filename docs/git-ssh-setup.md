---
layout: default
title: Git SSH Setup
---

# Git SSH Setup (Port 443)

[← Home](../)

---

## Generate SSH Key

<details>
<summary><strong>macOS / Linux</strong></summary>

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```
</details>

---

## SSH Config (Port 443)

<details>
<summary><strong>macOS / Linux</strong></summary>

Edit `~/.ssh/config`:

```
Host github.com
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile ~/.ssh/id_ed25519
```
</details>

<details>
<summary><strong>Windows</strong></summary>

Edit `C:\Users\<username>\.ssh\config`:

```
Host github.com
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile C:\Users\<username>\.ssh\id_ed25519
```
</details>

---

## Copy Public Key

<details>
<summary><strong>macOS</strong></summary>

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```
</details>

<details>
<summary><strong>Linux</strong></summary>

```bash
cat ~/.ssh/id_ed25519.pub
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
```
</details>

---

## Test Connection

```bash
ssh -T git@github.com
```
