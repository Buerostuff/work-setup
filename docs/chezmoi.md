# Dotfiles with chezmoi

One private repo keeps my agent rules, skills, tool settings and program list.
On a new machine, one command brings everything back - only the parts meant
for that machine type.

- [1. Concepts](#1-concepts)
- [2. Machine types](#2-machine-types)
- [3. Repo layout](#3-repo-layout)
- [4. New machine - step by step](#4-new-machine-step-by-step)
- [5. Daily use](#5-daily-use)
- [6. Choosing what syncs where](#6-choosing-what-syncs-where)
- [7. Programs list](#7-programs-list)
- [8. SSH](#8-ssh)
- [9. Never sync](#9-never-sync)
- [10. Cheat sheet](#10-cheat-sheet)
- [11. Troubleshooting](#11-troubleshooting)

---

## 1. Concepts

| Term | Meaning |
|---|---|
| **chezmoi** | Small tool (single exe) that copies files from a git repo into my home folder. |
| **Source dir** | The local clone of the repo: `~/.local/share/chezmoi`. I edit here. |
| **Target** | The real files in my home folder (e.g. `~/.config/opencode/AGENTS.md`). |
| **Template** (`.tmpl`) | File with `{{ if ... }}` blocks - content changes per machine. |
| **Script** (`run_...`) | PowerShell script chezmoi runs on `apply` (e.g. install programs). |
| **Machine type** | `personal`, `vdi` or `work-laptop`. Asked once per machine. |

```
 GitHub repo  ──chezmoi init/update──▶  source dir  ──chezmoi apply──▶  home folder
 (private)    ◀──────git push───────    (~/.local/share/chezmoi)  ◀──chezmoi re-add──
```

Only files I explicitly `chezmoi add` are managed. Everything else is untouched.

---

## 2. Machine types

| Type | Admin? | Programs installed via | Notes |
|---|---|---|---|
| `personal` | yes | `winget` | Full set. |
| `work-laptop` | maybe | `winget` (user scope) | No private stuff. |
| `vdi` | **no** | portable zips → `C:\Install\Programs` | Port 22 blocked → SSH via port 443. Check company policy before pushing from here. |

---

## 3. Repo layout

```
dotfiles/
├── .chezmoi.toml.tmpl                 # asks machine type once
├── .chezmoiignore                     # what NOT to place (per machine)
├── .chezmoidata/
│   └── packages.yaml                  # program list with machine tags
├── run_onchange_install-packages.ps1.tmpl   # installs programs from the list
├── dot_config/
│   └── opencode/
│       ├── AGENTS.md.tmpl             # global agent rules (templated)
│       ├── opencode.jsonc.tmpl
│       └── skill/...
├── dot_agents/skills/...              # shared skills
├── dot_claude/skills/...
├── dot_ssh/
│   └── config.tmpl                    # ssh config ONLY, never keys
├── Documents/PowerShell/Microsoft.PowerShell_profile.ps1.tmpl
└── docs/                              # this page (GitHub Pages)
```

Naming rules chezmoi uses:

| Repo name | Becomes |
|---|---|
| `dot_config` | `.config` |
| `file.tmpl` | `file` (rendered as template) |
| `private_x` | `x` with restricted permissions |
| `run_once_x.ps1` | runs once per machine |
| `run_onchange_x.ps1` | runs when its content changes |

---

## 4. New machine - step by step

### 4.1 Get chezmoi

**Personal / work laptop (admin or winget allowed):**

```powershell
winget install Git.Git twpayne.chezmoi
```

**VDI (no admin):** download the Windows zip from
<https://github.com/twpayne/chezmoi/releases>, unpack to
`C:\Install\Programs\chezmoi`, then add it to the user PATH:

```powershell
[Environment]::SetEnvironmentVariable('Path',
  [Environment]::GetEnvironmentVariable('Path','User') + ';C:\Install\Programs\chezmoi',
  'User')
```

Open a new terminal, check: `chezmoi --version`.

### 4.2 Pull the repo (HTTPS - no SSH key needed yet)

```powershell
chezmoi init https://github.com/<user>/dotfiles.git
```

A browser login opens (Git Credential Manager). chezmoi then asks:

```
Machine type: personal / vdi / work-laptop
```

### 4.3 Preview, then apply

```powershell
chezmoi diff      # what would change
chezmoi apply -v  # place files + run install script
```

### 4.4 Create the SSH key for this machine

See [8. SSH](#8-ssh).

### 4.5 Switch the repo to SSH (optional)

```powershell
chezmoi cd
git remote set-url origin git@github.com:<user>/dotfiles.git   # or github_vdi:<user>/dotfiles.git on VDI
exit
```

Done.

---

## 5. Daily use

| I want to... | Command |
|---|---|
| Start syncing a new file | `chezmoi add <path>` |
| Add as template (per-machine content) | `chezmoi add --template <path>` |
| Edit a managed file | `chezmoi edit <path>` then `chezmoi apply` |
| I edited the real file directly | `chezmoi re-add` |
| See differences | `chezmoi diff` |
| Save to GitHub | `chezmoi cd` → `git add <files>` → `git commit` → `git push` → `exit` |
| Get changes on another machine | `chezmoi update` (= git pull + apply) |
| Stop syncing a file (keeps it locally) | `chezmoi forget <path>` |
| List managed files | `chezmoi managed` |
| Show machine data | `chezmoi data` |

---

## 6. Choosing what syncs where

### 6.1 Ask machine type once - `.chezmoi.toml.tmpl`

```toml
{{- $machine := promptChoiceOnce . "machine" "Machine type" (list "personal" "vdi" "work-laptop") -}}
[data]
  machine = {{ $machine | quote }}
```

Change it later: `chezmoi init --prompt`, or edit `~/.config/chezmoi/chezmoi.toml`.

### 6.2 Skip whole files per machine - `.chezmoiignore`

```
README.md
docs/**

{{ if ne .machine "vdi" }}
.config/opencode/skill/windows-eperm-node-modules/**
{{ end }}

{{ if eq .machine "work-laptop" }}
Documents/private-notes/**
{{ end }}
```

(Paths here are **target** paths, i.e. `.config/...`, not `dot_config/...`.)

### 6.3 Different content inside one file - templates

`AGENTS.md.tmpl`:

```
# Global rules
... shared rules ...

{{ if eq .machine "vdi" }}
## Corporate DevVDI (no admin rights)
... VDI-only rules ...
{{ end }}
```

Paths that differ per machine:

```
{{ .chezmoi.homeDir }}/.mcp/ag-mcp/...
```

Test a template without applying:

```powershell
chezmoi execute-template < "$(chezmoi source-path)\dot_config\opencode\AGENTS.md.tmpl"
```

---

## 7. Programs list

Program **files** are not synced (too big, go stale). Only the **list** is.

### 7.1 `.chezmoidata/packages.yaml`

```yaml
packages:
  - name: Git
    winget: Git.Git
    on: [personal, work-laptop]
  - name: VS Code
    winget: Microsoft.VisualStudioCode
    zip: https://update.code.visualstudio.com/latest/win32-x64-archive/stable
    dir: Microsoft VS Code
    on: [personal, work-laptop, vdi]
  - name: Obsidian
    winget: Obsidian.Obsidian
    on: [personal, vdi]
  - name: Docker Desktop
    winget: Docker.DockerDesktop
    on: [personal]
  - name: ripgrep
    winget: BurntSushi.ripgrep.MSVC
    on: [personal, work-laptop, vdi]
```

- `winget` → used on `personal` / `work-laptop`.
- `zip` + `dir` → used on `vdi` (unpacked to `C:\Install\Programs\<dir>`).
- `on` → which machine types get it.

### 7.2 `run_onchange_install-packages.ps1.tmpl`

```powershell
# packages hash: {{ include ".chezmoidata/packages.yaml" | sha256sum }}
$machine = '{{ .machine }}'
$root    = 'C:\Install\Programs'

{{ range .packages -}}
{{ if has $.machine .on -}}
# --- {{ .name }} ---
{{ if and (ne $.machine "vdi") .winget -}}
winget install --id {{ .winget }} -e --scope user --accept-package-agreements --accept-source-agreements
{{- else if .zip -}}
$dest = Join-Path $root '{{ .dir }}'
if (-not (Test-Path $dest)) {
  $tmp = Join-Path $env:TEMP '{{ .dir }}.zip'
  Invoke-WebRequest '{{ .zip }}' -OutFile $tmp
  Expand-Archive $tmp -DestinationPath $dest -Force
  Remove-Item $tmp
}
{{- end }}
{{ end -}}
{{ end -}}
```

The hash comment makes chezmoi re-run the script only when the list changes.

### 7.3 Add a program

1. Find its id: `winget search <name>` (or the portable zip URL for VDI).
2. Add an entry to `packages.yaml` with the right `on:` tags.
3. `chezmoi apply` → it installs. Commit + push.

---

## 8. SSH

**Keys are never in the repo.** Each machine gets its own key, so a lost
laptop = delete one key on GitHub. Full key setup: [Git SSH Setup](git-ssh-setup.md).

```powershell
ssh-keygen -t ed25519 -C "<machine-name>"
Get-Content $HOME\.ssh\id_ed25519.pub
```

Paste into GitHub → *Settings → SSH and GPG keys → New SSH key*.

Only `~/.ssh/config` is synced (as template). VDI part (port 22 blocked):

```
{{ if eq .machine "vdi" }}
Host github_vdi
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile ~/.ssh/id_ed25519_work_vdi
    IdentitiesOnly yes
{{ end }}
```

Test:

```powershell
ssh -T git@github.com      # personal / work laptop
ssh -T github_vdi          # VDI
```

Clone on VDI: `git clone github_vdi:<owner>/<repo>.git`.

`.chezmoiignore` safety net:

```
.ssh/id_*
.ssh/known_hosts*
```

---

## 9. Never sync

- SSH private keys, tokens, passwords
- `.env`, `.env.*` files
- `node_modules`, caches, program binaries
- Internal hostnames / IPs (if the repo or this page is public)

Safety net in `.chezmoiignore`:

```
**/.env
**/.env.*
**/node_modules/**
```

---

## 10. Cheat sheet

```powershell
# new machine
chezmoi init https://github.com/<user>/dotfiles.git
chezmoi diff
chezmoi apply -v

# daily
chezmoi add <file>            # start tracking
chezmoi re-add                # pull local edits into repo
chezmoi update                # get + apply changes from GitHub
chezmoi cd                    # go to repo, then git add/commit/push, exit

# inspect
chezmoi managed
chezmoi data
chezmoi doctor
```

---

## 11. Troubleshooting

| Problem | Fix |
|---|---|
| `chezmoi` not found | Add its folder to user PATH, open a new terminal. |
| `Permission denied (publickey)` | Public key not on GitHub, or wrong host alias. `ssh -T ...` to test. |
| `port 22: Connection timed out` | Network blocks 22 → use `ssh.github.com` port 443 alias. |
| Wrong machine type | `chezmoi init --prompt` or edit `~/.config/chezmoi/chezmoi.toml`. |
| Install script did not run | It runs only when `packages.yaml` changes; force: `chezmoi state delete-bucket --bucket=entryState` then `chezmoi apply`. |
| Template error | `chezmoi execute-template` on the file to see the output. |
| Something looks off | `chezmoi doctor`. |
