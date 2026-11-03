# Dotfiles with chezmoi

One private repo, [`Buerostuff/dotfiles`](https://github.com/Buerostuff/dotfiles),
keeps my agent rules, skills, tool settings and program list. On a new machine
one command brings everything back - only the parts meant for that **profile**.

---

## 1. Concepts

| Term | Meaning |
|---|---|
| **chezmoi** | Small tool (single exe) that copies files from a git repo into my home folder. |
| **Source dir** | The repo on disk. VDI: `C:\Install\_github\dotfiles`. Elsewhere default: `~/.local/share/chezmoi`. I edit here. |
| **Target** | The real file in my home folder (e.g. `~/.config/opencode/AGENTS.md`). |
| **Template** (`.tmpl`) | File with `{{ if ... }}` blocks - content changes per profile / OS. |
| **Script** (`run_...`) | Script chezmoi runs on `apply` (install programs, copy settings). |
| **Profile** | Who uses the machine and what it may get. Asked once per machine. |
| **OS** | Detected automatically (`windows`, `darwin`, `linux`). Not a profile. |

```
 GitHub repo  ── git pull / chezmoi update ──▶  source dir  ── chezmoi apply ──▶  home folder
 (private)    ◀──────── git push ───────────   (the repo)   ◀── chezmoi re-add ──
```

Only files I explicitly `chezmoi add` are managed. Everything else is untouched.

---

## 2. Profiles

Profile = **trust level / purpose**. OS is separate and automatic, so there is no
"windows-personal" vs "mac-personal" - just `rg-personal` on any OS.

| Profile | Machine | Idea |
|---|---|---|
| `rg-personal` | my own laptops / PCs | everything |
| `rg-semi` | own use, not fully mine | like personal, but no git identity, no SSH |
| `rg-vdi` | company dev VDI (no admin) | full work dev setup |
| `rg-work` | company laptop | only Lintalist |
| `rg-collab` | shared with a colleague | dev tools + shell look, none of my rules |
| `rg-public` | shared / untrusted | shell look + core tools only |

### 2.1 Dotfiles per profile

Source of truth: `.chezmoidata/dotfiles.yaml`.

| Dotfile | personal | semi | vdi | work | collab | public |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| Agent rules `AGENTS.md` | ✓ | ✓ | ✓ (+VDI, IT tickets) | | | |
| Skills (`.agents`, `.claude`, opencode) | ✓ | ✓ | ✓ | | | |
| `opencode.jsonc` | ✓ | ✓ | ✓ | | | |
| `.gitconfig` (name + email) | ✓ | | ✓ | | | |
| `starship.toml` | ✓ | ✓ | ✓ | | ✓ | ✓ |
| PowerShell profile | ✓ | ✓ | ✓ | | ✓ | ✓ |
| VS Code keybindings + snippets | ✓ | ✓ | ✓ | | | |
| Lintalist settings + snippets | ✓ | ✓ | ✓ | ✓ | | |
| SSH config (`github_vdi`, devops) | | | ✓ | | | |
| Windows Terminal `settings.json` | ✓ | | ✓ | | | |

Not synced on purpose: VS Code `settings.json` (holds DB connection passwords),
SSH keys.

### 2.2 Programs per profile

Source of truth: `.chezmoidata/packages.yaml`.

| Program | personal | semi | vdi | work | collab | public |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| Git, PowerShell 7, Starship, ripgrep, VS Code | ✓ | ✓ | ✓ | | ✓ | ✓ |
| chezmoi | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Cursor, Zed | ✓ | ✓ | ✓ | | ✓ | |
| Node, Bun, Python 3.13, uv, Go, .NET 10, Docker | ✓ | ✓ | ✓ | | ✓ | |
| HeidiSQL, Azure Storage Explorer, WinSCP | ✓ | ✓ | ✓ | | | |
| Obsidian, PDF-XChange | ✓ | ✓ | ✓ | | | |
| Lintalist | ✓ | ✓ | ✓ | ✓ | | |

---

## 3. What is in the repo

```
dotfiles/
├── .chezmoi.toml.tmpl                    # asks profile, sets sourceDir, runs .ps1 with pwsh
├── .chezmoiignore                        # never-sync list + per-profile / per-OS skips
├── .chezmoidata/
│   ├── dotfiles.yaml                     # dotfile groups → profiles
│   └── packages.yaml                     # programs → profiles (winget / brew / VDI dir)
├── .chezmoiscripts/
│   ├── run_onchange_install-packages.ps1.tmpl       # Windows: winget, or VDI check
│   ├── run_onchange_install-packages.sh.tmpl        # macOS / Linux: brew
│   ├── run_onchange_after_pwsh-profile.ps1.tmpl     # puts pwsh profile in place
│   └── run_onchange_after_lintalist-settings.ps1.tmpl  # copies Lintalist settings
├── README.md                             # not placed in home
├── AppData/Roaming/Code/User/            # VS Code keybindings + snippets
├── dot_config/
│   ├── opencode/  AGENTS.md.tmpl, opencode.jsonc.tmpl, skill/
│   ├── powershell/Microsoft.PowerShell_profile.ps1.tmpl
│   ├── lintalist/ Settings.ini, *.ini, bundles/, local/
│   └── starship.toml
├── dot_agents/skills/...
├── dot_claude/skills/react-doctor/
├── dot_gitconfig
└── dot_ssh/config                        # VDI only, never keys
```

| Repo name | Becomes in home |
|---|---|
| `dot_config` | `.config` |
| `x.tmpl` | `x` (rendered per profile / OS) |
| `.chezmoiscripts/run_onchange_x.ps1.tmpl` | script, runs when its content changes |
| `run_onchange_after_...` | same, runs after files are placed |
| `.chezmoidata/*.yaml` | data for templates (`.packages`, `.dotfiles`) |

---

## 4. New machine - step by step

### 4.1 Get git + chezmoi

=== "Windows (winget)"

    ```powershell
    winget install Git.Git twpayne.chezmoi
    ```

=== "VDI (no admin)"

    Download the Windows zip from
    <https://github.com/twpayne/chezmoi/releases>, unpack to
    `C:\Install\Programs\chezmoi`, then add to the user PATH:

    ```powershell
    [Environment]::SetEnvironmentVariable('Path',
      [Environment]::GetEnvironmentVariable('Path','User') + ';C:\Install\Programs\chezmoi',
      'User')
    ```

    New terminal → `chezmoi --version`.

=== "macOS / Linux"

    Install Homebrew (<https://brew.sh>), then:

    ```bash
    brew install git chezmoi
    ```

### 4.2 Pull the repo and pick the profile (HTTPS - no SSH key needed yet)

=== "Windows / macOS / Linux"

    ```powershell
    chezmoi init https://github.com/Buerostuff/dotfiles.git
    ```

=== "VDI (repo in C:\Install\_github)"

    ```powershell
    git clone https://github.com/Buerostuff/dotfiles.git C:\Install\_github\dotfiles
    chezmoi init --source C:\Install\_github\dotfiles
    ```

A browser login opens (Git Credential Manager). chezmoi asks:

```
Profile: rg-personal / rg-semi / rg-vdi / rg-work / rg-collab / rg-public
```

Non-interactive shell: add `--promptChoice "Profile=rg-vdi"`.

### 4.3 Preview, then apply

```powershell
chezmoi diff      # what would change
chezmoi apply -v  # place files + run scripts (install programs, profile, Lintalist)
```

### 4.4 SSH key for this machine

See [8. SSH](#8-ssh).

### 4.5 Switch the repo to SSH (optional)

```powershell
chezmoi cd
git remote set-url origin git@github.com:Buerostuff/dotfiles.git   # VDI: github_vdi:Buerostuff/dotfiles.git
exit
```

---

## 5. How the profile choice works

### 5.1 Ask once - `.chezmoi.toml.tmpl`

```toml
{{- $profile := promptChoiceOnce . "profile" "Profile" (list "rg-personal" "rg-semi" "rg-vdi" "rg-work" "rg-collab" "rg-public") -}}
sourceDir = {{ .chezmoi.sourceDir | quote }}

[data]
  profile = {{ $profile | quote }}

[interpreters.ps1]
  command = "pwsh"
  args = ["-NoLogo", "-NoProfile", "-ExecutionPolicy", "Bypass"]
```

Show it: `chezmoi data`. Change it: `chezmoi init --prompt`, or edit
`~/.config/chezmoi/chezmoi.toml`.

### 5.2 Dotfile groups - `.chezmoidata/dotfiles.yaml`

```yaml
dotfiles:
  agents:     [rg-personal, rg-semi, rg-vdi]
  gitconfig:  [rg-personal, rg-vdi]
  lintalist:  [rg-personal, rg-semi, rg-vdi, rg-work]
  ssh:        [rg-vdi]
  # ...
```

To give a profile a dotfile: add the profile to that group. Done.

### 5.3 Skipping - `.chezmoiignore`

Each group skips its files when the profile is not listed:

```
{{ if not (has .profile .dotfiles.gitconfig) }}
.gitconfig
{{ end }}
```

Plus per-OS skips (`AppData/**`, Lintalist only on Windows) and the never-sync
list. Paths are **home** paths (`.config/...`), not repo paths.

### 5.4 Sections inside one file - `AGENTS.md.tmpl`

```
{{ if eq .profile "rg-vdi" -}}
## Corporate DevVDI (no admin rights)
...
{{ end -}}
```

| Section | personal / semi | vdi |
|---|:-:|:-:|
| Communication, Todo list, Git, Installing software, Env files | ✓ | ✓ |
| Cloning via `github_vdi`, .NET on the DevVDI, Corporate DevVDI, IT tickets | | ✓ |

### 5.5 Test another profile without changing this machine

```powershell
chezmoi managed --include files --override-data '{"profile":"rg-work"}'

'{"profile":"rg-personal"}' | Set-Content $env:TEMP\p.json
chezmoi execute-template --override-data-file $env:TEMP\p.json `
  --file "$(chezmoi source-path)\dot_config\opencode\AGENTS.md.tmpl"
```

---

## 6. Programs

Program **files** are not synced. Only the **list** is.

### 6.1 Entry format

```yaml
- name: VS Code
  winget: Microsoft.VisualStudioCode   # Windows (not VDI)
  brew: visual-studio-code             # macOS / Linux
  cask: true                           # GUI app → brew --cask (macOS only)
  dir: Microsoft VS Code               # VDI: C:\Install\Programs\<dir>
  url: https://code.visualstudio.com/#alt-downloads   # by-hand download
  on: [rg-personal, rg-semi, rg-vdi, rg-collab, rg-public]
```

### 6.2 What happens on `apply`

Scripts re-run **only when `packages.yaml` changes** (they embed its hash).

| Where | Behaviour |
|---|---|
| Windows (not VDI) | not found by `winget list` → `winget install --scope user`, fallback machine scope. No winget id (Lintalist) → listed "install by hand" if `C:\Install\Programs\<dir>` is missing. |
| VDI (`rg-vdi`) | never downloads. Lists missing `C:\Install\Programs\<dir>` with URL. |
| macOS | `brew install` / `brew install --cask`. No brew id → skipped. |
| Linux | `brew install` for CLI formulas; casks listed "install by hand". |

### 6.3 Add or remove a program

1. `winget search <name>` / `brew search <name>` for the ids.
2. Edit `packages.yaml` (`on:` decides who gets it).
3. `chezmoi apply -v`.
4. Commit + push.

---

## 7. Special cases

### 7.1 PowerShell profile

Source: `dot_config/powershell/Microsoft.PowerShell_profile.ps1.tmpl` →
`~/.config/powershell/` (the native path on macOS / Linux).

On Windows a script copies it to the real profile:

| Profile | Copied to | Why |
|---|---|---|
| `rg-vdi` | `$PSHOME` → `C:\Install\Programs\PowerShell\7\Microsoft.PowerShell_profile.ps1` | Group policy blocks scripts in `Documents` ("blocked by software restriction policies"). |
| others | `$PROFILE` (`Documents\PowerShell\...`) | normal place |

Content: Starship prompt; on VDI the .NET 10 PATH fix
(`C:\Install\Programs\dotnet` before the system SDK 8); `Sync-Dotfiles` (9.1).

### 7.2 Lintalist

Lintalist lives in `C:\Install\Programs\lintalist64` - outside home, so chezmoi
keeps a copy in `~/.config/lintalist` (`Settings.ini`, `AltPaste.ini`,
`LineFeed.ini`, `multicaret.ini`, `Sound.ini`, `bundles/default.txt`,
`local/local.txt`).

- **Repo → Lintalist:** `chezmoi apply` copies them into the install folder
  (only when they changed). Restart Lintalist.
- **Lintalist → repo:** automatic via `Sync-Dotfiles` (see 9.1). New bundles
  are reported; add them with "check all and sync". By hand: `Sync-Dotfiles`.

### 7.3 VS Code

Synced: `keybindings.json`, `snippets/`. **Not** `settings.json` - it holds DB
connections with passwords (chezmoi warned on `add`). Never add it.

### 7.4 Windows Terminal

`AppData/Local/Packages/Microsoft.WindowsTerminal_8wekyb3d8bbwe/LocalState/settings.json`
for `rg-personal` and `rg-vdi`. It contains VDI paths (`C:\Install\Programs\PowerShell\7\pwsh.exe`,
start folder `C:\Install\_github`) - on a personal machine use the same folders
or change them there in the repo file.

---

## 8. SSH

**Keys are never in the repo.** Each machine gets its own key, so a lost
laptop = delete one key on GitHub. Full key setup: [Git SSH Setup](git-ssh-setup.md).

```powershell
ssh-keygen -t ed25519 -C "<machine-name>"
Get-Content $HOME\.ssh\id_ed25519.pub | Set-Clipboard
```

Paste into GitHub → *Settings → SSH and GPG keys → New SSH key*.

`~/.ssh/config` is synced **only to `rg-vdi`** (`github_vdi` port-443 alias,
VDI key names). Other machines write their own.

```powershell
ssh -T git@github.com      # elsewhere
ssh -T github_vdi          # VDI (port 22 blocked)
```

Clone on VDI: `git clone github_vdi:<owner>/<repo>.git`.

---

## 9. Daily use

| I want to... | Command |
|---|---|
| Start syncing a new file | `chezmoi add <path>` (then add it to a group in `dotfiles.yaml` + `.chezmoiignore`) |
| Edit a managed file | `chezmoi edit <path>` then `chezmoi apply` |
| I edited a plain (non-template) file directly | `chezmoi re-add` |
| See differences | `chezmoi diff` |
| Save to GitHub | `chezmoi cd` → `git add <files>` → `git commit` → `git push` → `exit` |
| Get changes on another machine | `chezmoi update` (= git pull + apply) |
| Stop syncing a file (keeps it locally) | `chezmoi forget <path>` |
| List managed files | `chezmoi managed` |
| Show profile / OS | `chezmoi data` |

> **_NOTE:_** `AGENTS.md`, `opencode.jsonc` and the PowerShell profile are
> templates. Edit the `.tmpl` in the repo (or `chezmoi edit`), then
> `chezmoi apply`. Direct edits to the target are overwritten on the next apply.

### 9.1 Automatic: `Sync-Dotfiles`

Defined in the PowerShell profile (all profiles). On terminal start it runs
**in the background, at most once per hour**:

| Step | What |
|---|---|
| 1 | Repo must be clean - otherwise it only reports "uncommitted changes" (protects work in progress). |
| 2 | Lintalist: copies changed **tracked** files back from `C:\Install\Programs\lintalist64`. |
| 3 | `chezmoi re-add` - picks up changes to all tracked plain files (not templates). |
| 4 | Secret scan on changed files - hit → nothing committed, file named. |
| 5 | Commit only those paths: `chore(sync): dotfiles <date> from <machine>`. **No push.** |
| 6 | New untracked files in watched folders (`watch:` in `dotfiles.yaml`) and new Lintalist bundles → **only reported**. |

The result shows as one yellow line at the next terminal start, e.g.

```
dotfiles: 2 file(s) committed - push when ready | 1 new untracked: .agents/skills/my-skill - say 'check all and sync'
```

Run it now by hand: `Sync-Dotfiles`. Last result: `~/.cache/dotfiles-sync.txt`.

### 9.2 New files: "check all and sync" (with the AI agent)

Say **"check all and sync"** to the agent. The rule lives in `AGENTS.md`, so
every machine with agent rules knows it.

1. Agent checks (read-only): `chezmoi diff`, new files in watched folders
   (skills, VS Code snippets, Lintalist), unmanaged config of listed programs,
   installed vs `packages.yaml`, repo git status, password scan.
2. Agent asks per group: add or skip, which profiles get it.
3. Agent adds the picks, `chezmoi diff` is empty.
4. Agent shows the diff + commit message → I say yes → commit.
5. **I push.**

---

## 10. Never sync

- SSH private keys, tokens, passwords
- VS Code `settings.json` (DB connection passwords)
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
chezmoi edit <file>; chezmoi apply
chezmoi update
Sync-Dotfiles                 # commit tracked changes now (no push)
chezmoi cd                    # git push, then exit

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
| `init` hangs at "Profile?" | Non-interactive shell: `--promptChoice "Profile=rg-vdi"`. |
| Profile "blocked by software restriction policies" | VDI policy. Use `rg-vdi` (profile goes to `$PSHOME`), delete `Documents\PowerShell\Microsoft.PowerShell_profile.ps1`. |
| `chezmoi add` warns about a password | Do not commit it. Remove from the repo, add the path to `.chezmoiignore`. |
| `Permission denied (publickey)` | Public key not on GitHub, or wrong host alias. Test with `ssh -T ...`. |
| `port 22: Connection timed out` | Network blocks 22 → use the `github_vdi` (port 443) alias. |
| Whole file shows as changed in `diff` | Line endings (LF vs CRLF) differ. Keep the target's endings. |
| Script did not run | `run_onchange` runs only when content changes. Force: `chezmoi state delete-bucket --bucket=entryState`, then `chezmoi apply`. |
| Template error | `chezmoi execute-template --file <tmpl>` to see the output. |
| Something looks off | `chezmoi doctor`. |
