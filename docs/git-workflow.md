# Git Workflow for Professional Developers

## Branch Structure

```
main
  └── integration
        ├── feature/xyz
        ├── feature/abc
        └── fix/bug-123
```

| Branch | Purpose |
|--------|---------|
| `main` | Production-ready code (stable, tested) |
| `integration` | Staging area where features are merged and tested |
| `feature/*` | Individual work branches for new features |
| `fix/*` | Bug fix branches |

---

## Workflow Steps

### 1. Start New Work

```bash
git switch integration
git pull origin integration
git switch -c feature/my-feature
```

### 2. Develop and Commit

```bash
git add .
git commit -m "Add feature description"
```

### 3. Merge to Integration

```bash
git switch integration
git pull origin integration
git merge feature/my-feature
git push origin integration
```

### 4. Test

```bash
docker-compose up --build
```

### 5. Release to Production

```bash
git switch main
git pull origin main
git merge integration
git push origin main
```

### 6. Cleanup

```bash
git branch -d feature/my-feature
```

---

## Visual Workflow

```
feature/xyz → integration → main
     ↓             ↓          ↓
  develop        test     production
```

---

## Best Practices

1. **Never work directly on `main`** — always go through `integration`
2. **Never push to `main` without testing** on `integration` first
3. **Keep features small and focused** — easier to review and merge
4. **Merge early, merge often** — small merges are easier than large ones
5. **Pull before merge** — always update your branch before merging
6. **Update long-running feature branches** — merge `integration` into your feature branch regularly

---

## Handling Multiple Features

If you need to pause a feature:

```bash
# Save current work
git add .
git commit -m "WIP: feature progress"

# Switch to integration, start new feature
git switch integration
git pull origin integration
git switch -c feature/urgent-fix

# When done, go back to original feature
git switch feature/my-feature
git merge integration
```

---

## Resolving Merge Conflicts

When conflicts occur:

1. Open the conflicted file
2. Look for conflict markers:
   ```
   <<<<<<< HEAD
   (your branch content)
   =======
   (incoming branch content)
   >>>>>>> branch-name
   ```
3. Edit to keep desired content
4. Delete the markers (`<<<<<<<`, `=======`, `>>>>>>>`)
5. Save and commit:
   ```bash
   git add .
   git commit -m "Resolve merge conflict"
   ```

---

## Vim Keybindings for Conflict Resolution

### Modes

| Key | Mode | Description |
|-----|------|-------------|
| `i` | Insert | Start editing text |
| `Esc` | Normal | Stop editing, navigate |
| `:` | Command | Run commands (save, quit) |

### Navigation (Normal mode)

| Key | Action |
|-----|--------|
| `h` `j` `k` `l` | Left, Down, Up, Right |
| `gg` | Go to top of file |
| `G` | Go to bottom of file |
| `0` | Go to start of line |
| `$` | Go to end of line |
| `w` | Jump forward one word |
| `b` | Jump backward one word |
| `/text` | Search for "text" |
| `n` | Next search result |

### Editing (Normal mode)

| Key | Action |
|-----|--------|
| `i` | Insert before cursor |
| `a` | Insert after cursor |
| `o` | New line below and insert |
| `O` | New line above and insert |
| `x` | Delete character |
| `dd` | Delete entire line |
| `u` | Undo |
| `Ctrl+r` | Redo |

### Save and Quit (Normal mode)

| Command | Action |
|---------|--------|
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:q!` | Quit without saving |
| `ZZ` | Save and quit (shortcut) |

### Quick Conflict Resolution Workflow

1. Press `/<<<` then `Enter` to find conflict markers
2. Press `dd` to delete the marker line
3. Navigate with `j` and `k` to review content
4. Delete unwanted lines with `dd`
5. Delete remaining markers (`=======`, `>>>>>>>`)
6. Press `:wq` to save and exit
