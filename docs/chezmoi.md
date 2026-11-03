# Dotfiles with chezmoi

One private repo, [`Buerostuff/dotfiles`](https://github.com/Buerostuff/dotfiles),
keeps my agent rules, skills, tool settings and program list. On a new machine
one command brings everything back - only the parts meant for that machine type.

---

## 1. Concepts

| Term | Meaning |
|---|---|
| **chezmoi** | Small tool (single exe) that copies files from a git repo into my home folder. |
| **Source dir** | The repo on disk. On the VDI: `C:\Install\_github\dotfiles`. Default elsewhere: `~/.local/share/chezmoi`. I edit here. |
| **Target** | The real file in my home folder (e.g. `~/.config/opencode/AGENTS.md`). |
| **Template** (`.tmpl`) | File with `{{ if ... }}` blocks - content changes per machine. |
| **Script** (`run_...`) | PowerShell script chezmoi runs on `apply` (checks / installs programs). |
| **Machine type** | `personal`, `vdi` or `work-laptop`. Asked once per machine. |

```
 GitHub repo  ── git pull / chezmoi update ──▶  source dir  ── chezmoi apply ──▶  home folder
 (private)    ◀──────── git push ───────────   (the repo)   ◀── chezmoi re-add ──
```

Only files I explicitly `chezmoi add` are managed. Everything else is untouched.

---

## 2. Machine types

| Type | Admin? | Programs | Gets |
|---|---|---|---|
| `personal` | yes | auto-installed with `winget` | Shared rules only. No VDI / company sections. |
| `work-laptop` | maybe | auto-installed with `winget` (user scope first) | Shared rules + IT-ticket rules. |
| `vdi` | **no** | portable, by hand into `C:\Install\Programs`; script lists what is missing | Everything: VDI rules, .NET notes, `github_vdi` SSH config. |

---

## 3. What is in the repo

```
dotfiles/
├── .chezmoi.toml.tmpl            # asks machine type, sets sourceDir, runs .ps1 with pwsh
├── .chezmoiignore                # never-sync list + per-machine skips
├── .chezmoidata/
│   └── packages.yaml             # program list with machine tags
├── .chezmoiscripts/
│   └── run_onchange_install-packages.ps1.tmpl
├── README.md                     # not placed in home (ignored)
├── dot_config/
│   ├── opencode/
│   │   ├── AGENTS.md.tmpl        # global agent rules (sections per machine)
│   │   ├── opencode.jsonc.tmpl   # MCP path built from home dir
│   │   └── skill/windows-eperm-node-modules/
│   └── starship.toml
├── dot_agents/skills/...         # caveman, frontend-design, skill-creator, ...
├── dot_claude/skills/react-doctor/
├── dot_gitconfig
└── dot_ssh/config                # VDI only, never keys
```

| Repo name | Becomes in home |
|---|---|
| `dot_config` | `.config` |
| `x.tmpl` | `x` (rendered per machine) |
| `.chezmoiscripts/run_onchange_x.ps1.tmpl` | script, runs when its content changes |
| `.chezmoidata/*.yaml` | data usable in templates (`.packages`) |

---

## 4. First setup (how this VDI was done)

Done once, on the VDI, with an existing empty repo already cloned to
`C:\Install\_github\dotfiles`.

**1. chezmoi on PATH** (exe in `C:\Install\Programs\chezmoi`, no admin):

```powershell
[Environment]::SetEnvironmentVariable('Path',
  [Environment]::GetEnvironmentVariable('Path','User') + ';C:\Install\Programs\chezmoi',
  'User')
```

New terminal → `chezmoi --version`.

**2. Write `.chezmoi.toml.tmpl` in the repo:**

```toml
{{- $machine := promptChoiceOnce . "machine" "Machine type" (list "personal" "vdi" "work-laptop") -}}
sourceDir = {{ .chezmoi.sourceDir | quote }}

[data]
  machine = {{ $machine | quote }}

[interpreters.ps1]
  command = "pwsh"
  args = ["-NoLogo", "-NoProfile", "-ExecutionPolicy", "Bypass"]
```

**3. Point chezmoi at the repo and set the machine type:**

```powershell
chezmoi init --source C:\Install\_github\dotfiles --promptChoice "Machine type=vdi"
```

This writes `~/.config/chezmoi/chezmoi.toml` (sourceDir + `machine = "vdi"`).

**4. Add the files:**

```powershell
chezmoi add ~/.config/opencode/AGENTS.md ~/.config/opencode/opencode.jsonc `
            ~/.config/opencode/skill ~/.agents/skills ~/.claude/skills `
            ~/.ssh/config ~/.gitconfig ~/.config/starship.toml
```

**5. Turn per-machine files into templates** (rename to `.tmpl`, add
`{{ if eq .machine "vdi" }} ... {{ end }}` blocks - see [6](#6-choosing-what-syncs-where)).

**6. Check nothing changes on this machine, then apply:**

```powershell
chezmoi diff        # must be empty for files
chezmoi apply -v    # runs the package check script
```

**7. Commit and push** the repo (`git add <paths>`, `git commit`, `git push`).

---

## 5. New machine - step by step

### 5.1 Get git + chezmoi

=== "Personal / work laptop"

    ```powershell
    winget install Git.Git twpayne.chezmoi
    ```

=== "VDI (no admin)"

    Download the Windows zip from
    <https://github.com/twpayne/chezmoi/releases>, unpack to
    `C:\Install\Programs\chezmoi`, add to PATH (see [4](#4-first-setup-how-this-vdi-was-done) step 1).

### 5.2 Pull the repo (HTTPS - no SSH key needed yet)

=== "Personal / work laptop"

    ```powershell
    chezmoi init https://github.com/Buerostuff/dotfiles.git
    ```

    Clones to `~/.local/share/chezmoi`.

=== "VDI (repo in C:\Install\_github)"

    ```powershell
    git clone https://github.com/Buerostuff/dotfiles.git C:\Install\_github\dotfiles
    chezmoi init --source C:\Install\_github\dotfiles
    ```

A browser login opens (Git Credential Manager). chezmoi asks:

```
Machine type: personal / vdi / work-laptop
```

### 5.3 Preview, then apply

```powershell
chezmoi diff      # what would change
chezmoi apply -v  # place files + install / check programs
```

### 5.4 SSH key for this machine

See [8. SSH](#8-ssh).

### 5.5 Switch the repo to SSH (optional)

```powershell
chezmoi cd
git remote set-url origin git@github.com:Buerostuff/dotfiles.git   # VDI: github_vdi:Buerostuff/dotfiles.git
exit
```

---

## 6. Choosing what syncs where

### 6.1 Machine type

Set once by `.chezmoi.toml.tmpl` (above). Show it: `chezmoi data`.
Change it: `chezmoi init --prompt`, or edit `~/.config/chezmoi/chezmoi.toml`.

### 6.2 Skip whole files - `.chezmoiignore`

```
README.md

# never sync secrets or junk
.ssh/id_*
.ssh/known_hosts*
**/.env
**/.env.*
**/node_modules/**

# windows-only skill
{{ if ne .chezmoi.os "windows" }}
.config/opencode/skill/windows-eperm-node-modules/**
{{ end }}

# ssh config has VDI-only hosts and keys
{{ if ne .machine "vdi" }}
.ssh/config
{{ end }}
```

Paths here are **home** paths (`.config/...`), not repo paths (`dot_config/...`).

### 6.3 Sections inside one file - `AGENTS.md.tmpl`

| Section | personal | work-laptop | vdi |
|---|:-:|:-:|:-:|
| Communication, Todo list, Git, Installing software, Env files | ✓ | ✓ | ✓ |
| Cloning via `github_vdi` | | | ✓ |
| .NET on the DevVDI | | | ✓ |
| Corporate DevVDI (no admin) | | | ✓ |
| IT tickets | | ✓ | ✓ |

```
{{ if eq .machine "vdi" -}}
## Corporate DevVDI (no admin rights)
...
{{ end -}}
{{ if ne .machine "personal" -}}
## IT tickets ...
{{ end -}}
```

### 6.4 Paths per machine - `opencode.jsonc.tmpl`

```
"{{ .chezmoi.homeDir | replace "/" "\\\\" }}\\.mcp\\ag-mcp\\node_modules\\ag-mcp\\dist\\index.js"
```

### 6.5 Test a template for another machine type

```powershell
'{"machine":"personal"}' | Set-Content $env:TEMP\personal.json
chezmoi execute-template --override-data-file $env:TEMP\personal.json `
  --file "$(chezmoi source-path)\dot_config\opencode\AGENTS.md.tmpl"
```

---

## 7. Programs list

Program **files** are not synced (too big, go stale). Only the **list** is.

### 7.1 `.chezmoidata/packages.yaml`

```yaml
packages:
  - name: Git
    winget: Git.Git                      # personal / work-laptop: winget install
    dir: Git                             # VDI: folder under C:\Install\Programs
    url: https://git-scm.com/download/win  # VDI: where to download by hand
    on: [personal, vdi, work-laptop]     # who gets it
  - name: Lintalist
    dir: lintalist64                     # no winget id → always by hand
    url: https://github.com/lintalist/lintalist/releases
    on: [personal, vdi, work-laptop]
```

Current list: Git, PowerShell 7, chezmoi, Starship, ripgrep, VS Code, Cursor,
Zed, Node.js LTS, Bun, Python 3.13, uv, Go, .NET SDK 10, Erlang OTP, Docker,
HeidiSQL, Azure Storage Explorer, WinSCP, Obsidian, Zettlr, PDF-XChange Editor,
ShareX, Lintalist.

### 7.2 What the script does

`.chezmoiscripts/run_onchange_install-packages.ps1.tmpl` runs on `chezmoi apply`
**only when `packages.yaml` changes** (it embeds the file's hash).

| Machine | Behaviour |
|---|---|
| `personal`, `work-laptop` | For each package tagged for it: if `winget list` does not find it → `winget install --scope user`, fallback machine scope. No winget id → listed as "install by hand". |
| `vdi` | Never downloads. Checks `C:\Install\Programs\<dir>` exists; prints the missing ones with their URL. |

### 7.3 Add or remove a program

1. `winget search <name>` for the id (and the download URL for the VDI).
2. Edit `packages.yaml` (`on:` tags decide who gets it).
3. `chezmoi apply -v` → installs / reports.
4. Commit + push.

---

## 8. SSH

**Keys are never in the repo.** Each machine gets its own key, so a lost
laptop = delete one key on GitHub. Full key setup: [Git SSH Setup](git-ssh-setup.md).

```powershell
ssh-keygen -t ed25519 -C "<machine-name>"
Get-Content $HOME\.ssh\id_ed25519.pub | Set-Clipboard
```

Paste into GitHub → *Settings → SSH and GPG keys → New SSH key*.

`~/.ssh/config` is synced **only to the VDI** (it holds the `github_vdi`
port-443 alias and VDI key names). Other machines write their own.

```powershell
ssh -T git@github.com      # personal / work laptop
ssh -T github_vdi          # VDI (port 22 blocked)
```

Clone on VDI: `git clone github_vdi:<owner>/<repo>.git`.

---

## 9. Daily use

| I want to... | Command |
|---|---|
| Start syncing a new file | `chezmoi add <path>` |
| Add as template | `chezmoi add --template <path>` |
| Edit a managed file | `chezmoi edit <path>` then `chezmoi apply` |
| I edited the real file directly | `chezmoi re-add` (not for `.tmpl` files - edit the source) |
| See differences | `chezmoi diff` |
| Save to GitHub | `chezmoi cd` → `git add <files>` → `git commit` → `git push` → `exit` |
| Get changes on another machine | `chezmoi update` (= git pull + apply) |
| Stop syncing a file (keeps it locally) | `chezmoi forget <path>` |
| List managed files | `chezmoi managed` |
| Show machine data | `chezmoi data` |

> **_NOTE:_** `AGENTS.md` and `opencode.jsonc` are templates. Edit
> `dot_config/opencode/AGENTS.md.tmpl` in the repo (or `chezmoi edit`),
> then `chezmoi apply`. Editing `~/.config/opencode/AGENTS.md` directly is
> overwritten on the next apply.

---

## 10. Never sync

- SSH private keys, tokens, passwords
- `.env`, `.env.*` files
- `node_modules`, caches, program binaries

---

## 11. Cheat sheet

```powershell
# new machine
chezmoi init https://github.com/Buerostuff/dotfiles.git
chezmoi diff
chezmoi apply -v

# daily
chezmoi add <file>
chezmoi edit <file>
chezmoi apply
chezmoi update
chezmoi cd                    # git add / commit / push, then exit

# inspect
chezmoi managed
chezmoi data
chezmoi doctor
```

---

## 12. Troubleshooting

| Problem | Fix |
|---|---|
| `chezmoi` not found | Add its folder to user PATH, open a new terminal. |
| `init` hangs at "Machine type?" | Non-interactive shell: add `--promptChoice "Machine type=vdi"`. |
| `Permission denied (publickey)` | Public key not on GitHub, or wrong host alias. Test with `ssh -T ...`. |
| `port 22: Connection timed out` | Network blocks 22 → use the `github_vdi` (port 443) alias. |
| Wrong machine type | `chezmoi init --prompt` or edit `~/.config/chezmoi/chezmoi.toml`. |
| Whole file shows as changed in `diff` | Line endings (LF vs CRLF) differ between repo file and target. Keep the target's endings. |
| Install script did not run | It runs only when `packages.yaml` changes. Force: `chezmoi state delete-bucket --bucket=entryState` then `chezmoi apply`. |
| Template error | `chezmoi execute-template --file <tmpl>` to see the output. |
| Something looks off | `chezmoi doctor`. |
