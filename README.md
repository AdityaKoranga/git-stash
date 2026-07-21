# Git Stash Guide

## Overview

`git stash` is used to temporarily save your **uncommitted changes** so that you can switch branches or perform other Git operations without committing unfinished work.

Think of it as a temporary clipboard for your working directory.

---

# When Should You Use Git Stash?

Use `git stash` when:

- You are working on a feature but need to switch branches quickly.
- Someone asks you to fix an urgent bug.
- You need to pull the latest changes but have uncommitted work.
- You want to experiment without creating unnecessary commits.
- You are rebasing or merging and want a clean working tree.

---

# Understanding What Git Stash Saves

By default, Git stash saves:

✅ Modified tracked files

✅ Staged files

It does **NOT** save:

❌ Untracked files

❌ Ignored files

unless you explicitly ask it to.

---

# Basic Workflow

```text
Work on Feature A
        │
        ▼
git stash
        │
        ▼
Working directory becomes clean
        │
        ▼
Switch to another branch
        │
        ▼
Finish other work
        │
        ▼
Switch back
        │
        ▼
git stash apply / git stash pop
        │
        ▼
Continue Feature A
```

---

# Step 1 – Check Current Changes

Before stashing:

```bash
git status
```

Example:

```text
On branch feature/login

Changes not staged for commit:
    modified: app.py
    modified: config.yaml

Changes to be committed:
    modified: README.md
```

---

# Step 2 – Save Your Work

## Basic stash

```bash
git stash
```

Example output:

```text
Saved working directory and index state WIP on feature/login
```

Git stores your changes in a hidden stack.

---

## Stash with a Description (Recommended)

```bash
git stash push -m "WIP before switching to payment feature"
```

Later you'll know exactly why you created it.

---

# Step 3 – Verify Your Working Directory

```bash
git status
```

Expected output:

```text
nothing to commit, working tree clean
```

Now you can safely switch branches.

---

# Step 4 – Switch Branches

```bash
git checkout main
```

or

```bash
git switch main
```

Then continue your work.

---

# Step 5 – View All Stashes

```bash
git stash list
```

Example:

```text
stash@{0}: On feature/login: WIP before switching
stash@{1}: WIP on main
stash@{2}: On feature/api
```

Git treats stashes like a stack.

Newest stash:

```text
stash@{0}
```

Older stashes:

```text
stash@{1}
stash@{2}
...
```

---

# Step 6 – See What's Inside a Stash

Summary:

```bash
git stash show stash@{0}
```

Complete diff:

```bash
git stash show -p stash@{0}
```

Example:

```diff
+ Added login endpoint
- Removed debug statements
```

---

# Step 7 – Restore Your Work

There are two ways.

## Option A — Apply (Recommended)

```bash
git stash apply
```

or

```bash
git stash apply stash@{0}
```

Result:

- Restores changes
- Keeps the stash

```text
Before

stash@{0}

↓

Apply

↓

Changes restored

↓

stash still exists
```

Use this when you want an extra layer of safety.

---

## Option B — Pop

```bash
git stash pop
```

or

```bash
git stash pop stash@{0}
```

Result:

- Restores changes
- Deletes the stash

```text
Before

stash@{0}

↓

Pop

↓

Changes restored

↓

stash removed
```

Use this only if you're confident you won't need the stash again.

---

# Step 8 – Remove a Stash

Delete one stash:

```bash
git stash drop stash@{0}
```

Delete all stashes:

```bash
git stash clear
```

⚠️ **Warning:** This permanently removes every stash.

---

# Stashing Untracked Files

Normally Git ignores newly created files.

Example:

```text
new_script.py
```

won't be saved.

Include them with:

```bash
git stash -u
```

or

```bash
git stash --include-untracked
```

---

# Stashing Ignored Files

To stash even ignored files:

```bash
git stash -a
```

or

```bash
git stash --all
```

---

# Create a New Branch from a Stash

One of Git's most useful features.

```bash
git stash branch recover-work stash@{0}
```

Git will:

1. Create a new branch.
2. Restore the stash.
3. Remove the stash (if successful).

Very useful if you forgot which branch the stash belongs to.

---

# Recover After Applying

Since `apply` keeps the stash:

```text
git stash apply

↓

Oops!

↓

No problem.
```

You can apply it again because the stash still exists.

This is why many developers prefer `apply` over `pop`.

---

# Common Commands

## Save work

```bash
git stash
```

## Save with message

```bash
git stash push -m "working on ACM integration"
```

## Include untracked files

```bash
git stash -u
```

## View stashes

```bash
git stash list
```

## View full contents

```bash
git stash show -p stash@{0}
```

## Restore but keep stash

```bash
git stash apply stash@{0}
```

## Restore and remove stash

```bash
git stash pop stash@{0}
```

## Delete one stash

```bash
git stash drop stash@{0}
```

## Delete every stash

```bash
git stash clear
```

## Create branch from stash

```bash
git stash branch recover-work stash@{0}
```

---

# Best Practices

- ✅ Use descriptive stash messages (`git stash push -m "..."`).
- ✅ Prefer `git stash apply` first, then `git stash drop` after verifying your work. This is safer than `pop`.
- ✅ Use `git stash list` periodically to clean up old stashes.
- ✅ Use `git stash -u` if you've created new files that need to be preserved.
- ❌ Avoid `git stash clear` unless you're absolutely certain you no longer need any stashed work.

---

# Quick Reference

| Task | Command |
|------|---------|
| Save changes | `git stash` |
| Save with message | `git stash push -m "message"` |
| Save including untracked files | `git stash -u` |
| List stashes | `git stash list` |
| Show stash summary | `git stash show stash@{0}` |
| Show full stash diff | `git stash show -p stash@{0}` |
| Restore without deleting | `git stash apply stash@{0}` |
| Restore and delete | `git stash pop stash@{0}` |
| Delete one stash | `git stash drop stash@{0}` |
| Delete all stashes | `git stash clear` |
| Create branch from stash | `git stash branch <branch-name> stash@{0}` |

---

# Recommended Workflow

```bash
# Check current changes
git status

# Save your work
git stash push -m "WIP before switching branches"

# Switch branches
git switch another-branch

# Do other work...

# Return to your original branch
git switch my-feature-branch

# Restore your work (recommended)
git stash apply stash@{0}

# Verify everything looks correct
git status

# Remove the stash once you're satisfied
git stash drop stash@{0}
```

This workflow minimizes the risk of accidentally losing work while still letting you switch contexts quickly.
