# Git Commands

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

## Daily

| Command | What it does |
|---------|--------------|
| `git status` | Shows which files are changed, staged, or untracked |
| `git add -A` | Stages all changes (new, modified, deleted files) |
| `git commit -m "message"` | Saves staged changes with a description |
| `git push` | Uploads your commits to GitHub |
| `git pull` | Downloads latest changes from GitHub |

```bash
git status
git add -A && git commit -m "message"
git push
git pull
```

---

## Branching

| Command | What it does |
|---------|--------------|
| `git branch` | Lists all local branches |
| `git switch <branch>` | Switches to an existing branch |
| `git switch -c <branch>` | Creates a new branch and switches to it |
| `git checkout <branch>` | Old way to switch branches (still works) |
| `git checkout -b <branch>` | Old way to create & switch (still works) |
| `git branch -d <branch>` | Deletes a local branch |
| `git push origin -d <branch>` | Deletes a branch on GitHub |

```bash
# Recommended (git switch)
git branch                    # list all branches
git switch <branch>           # switch to branch
git switch -c <branch>        # create & switch

# Alternative (git checkout)
git checkout <branch>         # switch to branch
git checkout -b <branch>      # create & switch

# Delete
git branch -d <branch>        # delete local
git push origin -d <branch>   # delete remote
```

---

## Stash

| Command | What it does |
|---------|--------------|
| `git stash` | Temporarily saves your uncommitted changes |
| `git stash pop` | Restores your stashed changes and removes from stash |
| `git stash list` | Shows all stashed changes |

```bash
git stash
git stash pop
git stash list
```

---

## Undo

| Command | What it does |
|---------|--------------|
| `git restore <file>` | Discards changes in a file (back to last commit) |
| `git restore --staged <file>` | Unstages a file (keeps changes in working directory) |
| `git reset --soft HEAD~1` | Undoes last commit but keeps changes staged |
| `git reset --hard HEAD~1` | Undoes last commit and discards all changes |
| `git commit --amend` | Edits the last commit message or adds forgotten files |

```bash
git restore <file>            # discard changes
git restore --staged <file>   # unstage
git reset --soft HEAD~1       # undo commit, keep staged
git reset --hard HEAD~1       # undo commit, discard all
git commit --amend            # edit last commit
```

---

## Log

| Command | What it does |
|---------|--------------|
| `git log --oneline` | Shows commit history in one line per commit |
| `git log --oneline --graph --all` | Shows visual branch history with all branches |

```bash
git log --oneline
git log --oneline --graph --all
```
