# Dotfiles - Quick Start

Plain steps. Copy, paste, done. Details and the "why" are in
[Dotfiles reference](chezmoi.md).

---

## Which profile am I?

Answer one question: **whose machine is this?**

| The machine is... | Pick |
|---|---|
| Mine (my own laptop / PC / Mac) | `rg-personal` |
| Mine to use, but not really mine (family PC, borrowed) | `rg-semi` |
| The company dev VDI | `rg-vdi` |
| The company laptop | `rg-work` (only Lintalist) |
| Shared with a colleague | `rg-collab` |
| Public / untrusted | `rg-public` |

Unsure? Pick the **smaller** one. You can change it later ([below](#change-the-profile-later)).

---

## New personal Windows laptop

**1. Open PowerShell** (Start → type `PowerShell` → Enter).

**2. Install git and chezmoi:**

```powershell
winget install Git.Git twpayne.chezmoi
```

Close PowerShell, open it again.

**3. Get my setup:**

```powershell
chezmoi init https://github.com/Buerostuff/dotfiles.git
```

- A browser opens → log in to GitHub (account **Buerostuff**).
- It asks `Profile` → type `rg-personal` → Enter.

**4. Look before it changes anything:**

```powershell
chezmoi diff
```

Scroll with Space, quit with `q`. It only shows - nothing changes yet.

**5. Apply:**

```powershell
chezmoi apply -v
```

This places all settings and installs the programs with winget.
Takes a while. Some installers may ask "Allow changes?" → Yes.

At the end you may see a yellow list "Install by hand" (e.g. Lintalist).
Download those from the shown link into `C:\Install\Programs\<name>`.

**6. Make an SSH key for this laptop** (to push later):

```powershell
ssh-keygen -t ed25519 -C "personal-laptop"
```

Press Enter 3 times (or set a passphrase - remember it!).

```powershell
Get-Content $HOME\.ssh\id_ed25519.pub | Set-Clipboard
```

GitHub → [Settings → SSH keys](https://github.com/settings/keys) → **New SSH key** →
title "personal-laptop" → paste → **Add**.

Test:

```powershell
ssh -T git@github.com
```

Shows `Hi Buerostuff!` → good.

**7. Close and open the terminal.** You should see the Starship prompt. Done.

---

## New Mac

**1. Open Terminal** (Cmd+Space → `Terminal`).

**2. Install Homebrew** (copy the line from <https://brew.sh>), then:

```bash
brew install git chezmoi
```

**3–5. Same as Windows:**

```bash
chezmoi init https://github.com/Buerostuff/dotfiles.git   # Profile: rg-personal
chezmoi diff
chezmoi apply -v
```

Windows-only things (Lintalist, VS Code paths, Windows Terminal) are skipped automatically.

**6. SSH key:**

```bash
ssh-keygen -t ed25519 -C "macbook"
pbcopy < ~/.ssh/id_ed25519.pub
```

Paste on GitHub like above. Test: `ssh -T git@github.com`.

---

## New company VDI

No admin here, so a few extra steps.

**1. chezmoi:** download the Windows zip from
<https://github.com/twpayne/chezmoi/releases> (file `chezmoi_..._windows_amd64.zip`),
unpack into `C:\Install\Programs\chezmoi`.

**2. Put it on PATH:**

```powershell
[Environment]::SetEnvironmentVariable('Path',
  [Environment]::GetEnvironmentVariable('Path','User') + ';C:\Install\Programs\chezmoi',
  'User')
```

Close PowerShell, open it again. Check: `chezmoi --version`.

**3. Get the repo and set up:**

```powershell
git clone https://github.com/Buerostuff/dotfiles.git C:\Install\_github\dotfiles
chezmoi init --source C:\Install\_github\dotfiles
```

Profile → `rg-vdi`.

**4. Look, then apply:**

```powershell
chezmoi diff
chezmoi apply -v
```

On the VDI nothing gets downloaded - you get a yellow list of programs
missing in `C:\Install\Programs`. Install those by hand (links are shown).

**5. SSH** (port 22 is blocked here): see [Git SSH Setup](git-ssh-setup.md),
use the `github_vdi` alias. Test: `ssh -T github_vdi`.

**6. New terminal** → Starship prompt, `dotnet --version` shows 10.x. Done.

---

## New company laptop

```powershell
winget install twpayne.chezmoi
```

New PowerShell, then:

```powershell
chezmoi init https://github.com/Buerostuff/dotfiles.git   # Profile: rg-work
chezmoi apply -v
```

Only Lintalist settings come over. Install Lintalist by hand into
`C:\Install\Programs\lintalist64` (link is shown), then `chezmoi apply` once more.

---

## Every day - what do I do?

**Nothing, mostly.**

| I... | What happens / what I do |
|---|---|
| change a setting (Starship, Terminal, Lintalist snippet, VS Code keys) | Auto-saved and committed within an hour (on the next terminal start). A yellow line says so. |
| see `push when ready` | `cd` to the dotfiles repo → `git push` |
| see `new untracked ... say 'check all and sync'` | Tell the AI agent **"check all and sync"** → it asks what to add → I say yes → it commits → I push. |
| see `possible secret in ...` | Don't push. Open the file, remove the password, run `Sync-Dotfiles`. |
| see `repo has uncommitted changes` | I edited the repo by hand. Commit or undo it there. |
| want my changes from another machine here | `chezmoi update` |

Where is the repo?

```powershell
chezmoi cd      # opens a shell in the repo; type exit to leave
```

---

## Common tasks

### Add a new config file to sync

```powershell
chezmoi add ~/.config/sometool/config.toml
```

Then tell the agent "check all and sync" so it puts it in the right profile group.
Or do it yourself: add it to `.chezmoidata/dotfiles.yaml` + `.chezmoiignore`.

### Add a program to install everywhere

Open `.chezmoidata/packages.yaml` in the repo, copy an entry, change it:

```yaml
  - name: 7-Zip
    winget: 7zip.7zip          # find with: winget search 7zip
    brew: sevenzip             # find with: brew search 7zip (Mac/Linux)
    dir: 7-Zip                 # VDI folder name
    url: https://www.7-zip.org/download.html
    on: [rg-personal, rg-vdi]  # who gets it
```

Then:

```powershell
chezmoi apply -v
```

Commit + push.

### Change my agent rules

```powershell
chezmoi edit ~/.config/opencode/AGENTS.md
chezmoi apply
```

(Don't edit `~/.config/opencode/AGENTS.md` directly - it gets overwritten.)

### Change the profile later

```powershell
chezmoi init --prompt
chezmoi apply -v
```

### Stop syncing a file

```powershell
chezmoi forget ~/.config/sometool/config.toml
```

The file stays on the machine, just leaves the repo.

---

## Something went wrong

| I see | Do |
|---|---|
| `chezmoi: command not found` / not recognized | Close and reopen the terminal. VDI: redo the PATH step. |
| `Permission denied (publickey)` | Wrong SSH passphrase, or the key is not on GitHub. Test `ssh -T git@github.com` (VDI: `ssh -T github_vdi`). |
| Red error at terminal start | `chezmoi apply -v` again. Still red → [reference troubleshooting](chezmoi.md#12-troubleshooting). |
| It installed something I don't want here | Remove that profile from the program's `on:` in `packages.yaml`, uninstall by hand. |
| No idea | `chezmoi doctor` and ask the AI agent. |
