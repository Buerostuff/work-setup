# Git Commands Reference

## Clone

=== "SSH"

    ```bash
    git clone git@github.com:<username>/<repo>.git
    ```

    > **_NOTE:_** If you have multiple SSH profiles, replace `github.com` with your SSH config host (e.g., `github_vdi`). Check your `~/.ssh/config` file.

=== "HTTPS"

    ```bash
    git clone https://github.com/<username>/<repo>.git
    ```

---

## Push Existing Local Project to GitHub

If you already have a local project with `git init`, connect it to GitHub:

=== "SSH"

    ```bash
    git remote add origin git@github.com:<username>/<repo>.git
    git branch -M main
    git push -u origin main
    ```

    > **_NOTE:_** If you have multiple SSH profiles, replace `github.com` with your SSH config host (e.g., `github_vdi`). Check your `~/.ssh/config` file.

=== "HTTPS"

    ```bash
    git remote add origin https://github.com/<username>/<repo>.git
    git branch -M main
    git push -u origin main
    ```

> **_NOTE:_** Create an empty repository on GitHub first (without README or .gitignore) before pushing.

---

## Branch Management

| Command | Description |
|---------|-------------|
| `git switch <branch>` | Switch to an existing branch |
| `git switch -c <branch>` | Create and switch to a new branch |
| `git switch -` | Switch to the previous branch |
| `git branch` | List local branches |
| `git branch -a` | List all branches (local and remote) |
| `git branch -d <branch>` | Delete a branch (safe, checks if merged) |
| `git branch -D <branch>` | Force delete a branch (even if not merged) |
| `git branch -m <old> <new>` | Rename a branch |

### Flags for `git switch`

| Flag | Description |
|------|-------------|
| `-c` | Create a new branch and switch to it |
| `-` | Switch to the previously checked out branch |
| `--discard-changes` | Discard local changes when switching |

---

## Committing

| Command | Description |
|---------|-------------|
| `git add .` | Stage all changes in current directory |
| `git add -A` | Stage all changes (entire repo) |
| `git add <file>` | Stage a specific file |
| `git commit -m "message"` | Commit with a message |
| `git status` | Show working tree status |
| `git diff` | Show unstaged changes |

### One-liner (Add + Commit)

```bash
git add -A && git commit -m "Your message here"
```

Or using PowerShell 5.x (use semicolon):

```powershell
git add -A; git commit -m "Your message here"
```

### Flags for `git add`

| Flag | Description |
|------|-------------|
| `-A` | Stage **all** changes (new, modified, deleted) in entire repo |
| `.` | Stage all changes in current directory and subdirectories |
| `-p` | Interactive staging (choose which hunks to stage) |
| `-u` | Stage only modified and deleted files (not new files) |

### Flags for `git commit`

| Flag | Description |
|------|-------------|
| `-m "message"` | Provide commit message inline |
| `-a` | Automatically stage modified files (not new files) |
| `-am "message"` | Combine `-a` and `-m` (stage modified + commit) |
| `--amend` | Modify the last commit |

---

## Merging

| Command | Description |
|---------|-------------|
| `git merge <branch>` | Merge a branch into current branch |
| `git merge --abort` | Cancel a merge in progress |

### Flags for `git merge`

| Flag | Description |
|------|-------------|
| `--no-ff` | Create a merge commit even for fast-forward |
| `--abort` | Abort the current merge operation |
| `--squash` | Squash all commits into one (no auto-commit) |

---

## Remote Operations

| Command | Description |
|---------|-------------|
| `git pull origin <branch>` | Fetch and merge from remote |
| `git push origin <branch>` | Push branch to remote |
| `git push -u origin <branch>` | Push and set upstream tracking |
| `git push origin --delete <branch>` | Delete a remote branch |

### Flags for `git push`

| Flag | Description |
|------|-------------|
| `-u` | Set upstream tracking for the branch |
| `--delete` | Delete a branch on the remote |

### Flags for `git pull`

| Flag | Description |
|------|-------------|
| `--rebase` | Rebase instead of merge when pulling |

---

## Stash

| Command | Description |
|---------|-------------|
| `git stash` | Temporarily save uncommitted changes |
| `git stash pop` | Restore stashed changes and remove from stash |
| `git stash list` | Show all stashed changes |
| `git stash drop` | Delete most recent stash |

---

## Undo

| Command | Description |
|---------|-------------|
| `git restore <file>` | Discard changes in a file (back to last commit) |
| `git restore --staged <file>` | Unstage a file (keeps changes in working directory) |
| `git reset --soft HEAD~1` | Undo last commit but keep changes staged |
| `git reset --hard HEAD~1` | Undo last commit and discard all changes |
| `git commit --amend` | Edit the last commit message or add forgotten files |

---

## Tagging (for releases)

| Command | Description |
|---------|-------------|
| `git tag -a v1.0.0 -m "message"` | Create an annotated tag |
| `git push origin v1.0.0` | Push a tag to remote |
| `git tag` | List all tags |
| `git tag -d v1.0.0` | Delete a local tag |

### Flags for `git tag`

| Flag | Description |
|------|-------------|
| `-a` | Create an annotated tag |
| `-m "message"` | Tag message |
| `-d` | Delete a tag |

---

## Log

| Command | Description |
|---------|-------------|
| `git log --oneline` | Compact commit history |
| `git log --oneline --graph --all` | Visual branch history |
| `git log -n 5` | Show last 5 commits |
| `git log --author="name"` | Filter by author |
