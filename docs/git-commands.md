---
layout: default
title: Git Commands
---

# Git Commands

[← Home](../)

---

## Daily

```bash
git status
git add .
git commit -m "message"
git push
git pull
```

---

## Branching

```bash
git branch                    # list
git checkout -b <branch>      # create & switch
git checkout <branch>         # switch
git branch -d <branch>        # delete local
git push origin -d <branch>   # delete remote
```

---

## Stash

```bash
git stash
git stash pop
git stash list
```

---

## Undo

```bash
git restore <file>            # discard changes
git restore --staged <file>   # unstage
git reset --soft HEAD~1       # undo commit, keep staged
git reset --hard HEAD~1       # undo commit, discard all
git commit --amend            # edit last commit
```

---

## Log

```bash
git log --oneline
git log --oneline --graph --all
```
