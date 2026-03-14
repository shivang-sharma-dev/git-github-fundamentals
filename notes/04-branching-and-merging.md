# 04 — Branching and Merging

> Branching is Git's killer feature. Understanding it deeply separates juniors from seniors in DevOps interviews.

---

## What is a Branch?

A branch is simply a **lightweight movable pointer** to a commit. Nothing more.

When you create a branch, Git creates a new file in `.git/refs/heads/` containing the hash of the current commit. No files are copied. No history is duplicated. It takes milliseconds regardless of repository size.

```bash
# Proof: a branch is just a file with a hash
cat .git/refs/heads/main
# a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0
```

---

## Branch Operations

```bash
# List all local branches (* marks the current branch)
git branch

# List remote branches
git branch -r

# List ALL branches (local + remote)
git branch -a

# Create a new branch (stays on current branch)
git branch feature-login

# Switch to an existing branch
git switch feature-login
# (older syntax, still works) git checkout feature-login

# Create AND switch in one step
git switch -c feature-login
# (older syntax) git checkout -b feature-login

# Create a branch from a specific commit or tag
git switch -c hotfix v1.2.0
git switch -c debug-branch a1b2c3d

# Rename current branch
git branch -m new-name

# Rename another branch
git branch -m old-name new-name

# Delete a branch (safe — refuses if branch isn't merged)
git branch -d feature-login

# Force delete (even if not merged — data loss risk)
git branch -D abandoned-experiment

# See which branches are merged into current
git branch --merged

# See which branches are NOT yet merged
git branch --no-merged
```

---

## Understanding Merge Strategies

When you merge branches, Git chooses a strategy based on the commit history.

### Fast-Forward Merge

Happens when the target branch has not diverged — it's directly behind the source branch in history. Git simply moves the pointer forward.

```
Before:
  main:    A ── B ── C
                      ↑
  feature:            C ── D ── E
                                ↑

After (git merge feature while on main):
  main:    A ── B ── C ── D ── E
                                ↑
  (no merge commit created — just moved the pointer)
```

```bash
git switch main
git merge feature-login
# "Fast-forward" appears in output
```

Fast-forward is clean (linear history) but loses the information that a feature branch existed.

**Prevent fast-forward (always create a merge commit):**
```bash
git merge --no-ff feature-login
```

This always creates a merge commit, preserving the fact that these commits were a feature branch.

---

### 3-Way Merge (True Merge)

Happens when both branches have diverged — both have commits the other doesn't have. Git uses three commits: the two branch tips and their common ancestor.

```
Before:
  main:    A ── B ── C ── D
                \
  feature:       E ── F ── G

After (git merge feature while on main):
  main:    A ── B ── C ── D ── M  (M = merge commit, has two parents: D and G)
                \             /
  feature:       E ── F ── G
```

Git creates a new **merge commit** that has two parent commits. This commit ties the histories together.

```bash
git switch main
git merge feature-login
# Git opens your editor to write a merge commit message
```

---

## Merge Conflicts

A merge conflict happens when two branches changed the same part of the same file. Git can't decide which version to keep — it needs you to decide.

### When conflicts happen
```bash
git merge feature-branch
# CONFLICT (content): Merge conflict in app.py
# Automatic merge failed; fix conflicts and then commit the result.
```

### What a conflict looks like inside the file

```python
def greet():
<<<<<<< HEAD
    return "Hello, World!"
=======
    return "Hey there, World!"
>>>>>>> feature-branch
```

- `<<<<<<< HEAD` — start of your current branch's version
- `=======` — separator between the two versions
- `>>>>>>> feature-branch` — end of the incoming branch's version

### How to resolve conflicts

**Step 1:** Find all conflicted files
```bash
git status
# Both modified: app.py
```

**Step 2:** Open each conflicted file and decide what to keep. You have three choices:
- Keep your version (HEAD)
- Keep their version (incoming)
- Keep both (merge the content manually)

After editing, the file should have NO conflict markers (`<<<<`, `====`, `>>>>`).

```python
# Final resolved version:
def greet():
    return "Hello there, World!"  # manually merged
```

**Step 3:** Mark as resolved and complete the merge
```bash
git add app.py          # mark this file as resolved
git add other-file.py   # resolve all conflicted files first
git commit              # complete the merge (Git pre-fills the merge commit message)
```

**Abort a merge (go back to before you started):**
```bash
git merge --abort
```

### Tools to help with conflicts

```bash
# Use a visual merge tool
git mergetool

# Configure a specific tool (e.g., VS Code)
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

Most editors (VS Code, IntelliJ) show conflicts with visual "Accept Current / Accept Incoming / Accept Both" buttons — much easier than editing manually.

---

## Rebase — Rewriting History

Rebase moves or replays your commits on top of another branch, creating a **linear history**.

```
Before rebase:
  main:    A ── B ── C ── D
                \
  feature:       E ── F

git rebase main (while on feature):

After rebase:
  main:    A ── B ── C ── D
                           \
  feature:                  E' ── F'
  (E' and F' are new commits — same changes, new hashes)
```

The original commits E and F are replaced with E' and F' (new hash, same changes). The feature branch now starts from the latest main commit.

```bash
# Rebase feature branch on top of main
git switch feature-login
git rebase main

# Then merge back (will be fast-forward since history is linear)
git switch main
git merge feature-login   # fast-forward!
```

### Interactive Rebase — Your History Editor

Interactive rebase lets you rewrite, squash, reorder, or remove commits before sharing them.

```bash
# Rewrite the last 3 commits interactively
git rebase -i HEAD~3

# Rebase interactively from a specific commit
git rebase -i a1b2c3d
```

Git opens an editor showing your commits:
```
pick a1b2c3 feat: start login page
pick b2c3d4 fix: typo
pick c3d4e5 fix: another typo
pick d4e5f6 feat: complete login page
```

Change `pick` to an action:

| Action | Shorthand | What it does |
|---|---|---|
| `pick` | `p` | Keep the commit as-is |
| `reword` | `r` | Keep commit, but edit the message |
| `edit` | `e` | Pause to amend the commit |
| `squash` | `s` | Combine with previous commit, merge messages |
| `fixup` | `f` | Combine with previous commit, discard this message |
| `drop` | `d` | Delete this commit entirely |

**Example — squash 4 commits into 1:**
```
pick a1b2c3 feat: start login page
squash b2c3d4 fix: typo
squash c3d4e5 fix: another typo
squash d4e5f6 feat: complete login page
```
Result: one clean commit "feat: complete login page" (or whatever you write in the editor that opens next).

### Handling rebase conflicts
```bash
# During rebase, if a conflict occurs:
# 1. Resolve the conflict in the file
git add conflicted-file.py
git rebase --continue

# OR abort and go back to before
git rebase --abort
```

---

## The Golden Rule of Rebase

**Never rebase commits that have been pushed to a shared/public branch.**

Rebase rewrites commit hashes. If someone else has pulled those commits and you rebase, their history diverges from yours and you create a mess.

```
Safe to rebase:    Your local feature branch (not yet pushed)
Safe to rebase:    A private branch only you use

NEVER rebase:      main
NEVER rebase:      develop
NEVER rebase:      Any branch your teammates are working on
```

---

## Cherry-Pick — Take a Specific Commit

Cherry-pick applies the changes from a specific commit onto your current branch. Useful for pulling a bug fix from one branch to another without merging everything.

```bash
# Apply one specific commit to current branch
git cherry-pick a1b2c3d

# Apply multiple commits
git cherry-pick a1b2c3d e4f5a6b

# Apply a range of commits
git cherry-pick a1b2c3d..e4f5a6b

# Cherry-pick without committing (stage the changes only)
git cherry-pick --no-commit a1b2c3d

# Abort if conflicts
git cherry-pick --abort
```

**Common use case:**
```bash
# Bug was fixed on feature branch, need it on main immediately
git switch main
git cherry-pick abc123   # grab just that fix commit
```

---

## Rebase vs Merge — When to Use Which

| Scenario | Use |
|---|---|
| Merging a completed feature into main | `merge --no-ff` (preserves feature context) |
| Updating your feature branch with latest main | `rebase` (keeps history clean) |
| The feature branch is shared with teammates | `merge` (never rebase shared branches) |
| Cleaning up local messy commits before PR | `rebase -i` (squash/fixup) |
| Hotfix needs to go to both main and release | `cherry-pick` |
| Any public/shared branch | `merge` — always |

**Simple rule:** Rebase to clean up your private local work. Merge to combine completed work into shared branches.

---

## Viewing Branch History

```bash
# Beautiful visual graph of all branches
git log --oneline --graph --all --decorate

# Show commits on feature but not on main
git log main..feature-branch

# Show commits that are on either branch but not both (symmetric difference)
git log main...feature-branch

# Which branch contains a specific commit?
git branch --contains a1b2c3d
```

---

## Branch Naming Conventions

Consistent naming makes large repos manageable:

```
feature/user-authentication
feature/payment-gateway

fix/login-redirect-bug
fix/null-pointer-exception

hotfix/critical-payment-bug

release/v1.2.0
release/v2.0.0-beta

chore/update-dependencies
chore/cleanup-unused-imports

docs/api-reference
```

Most CI/CD systems trigger different pipelines based on branch name patterns — naming matters.

---

## Common Branching Mistakes

| Mistake | Consequence | Fix |
|---|---|---|
| Working directly on `main` | Changes aren't reviewed, breaks CI | Always branch for features and fixes |
| Rebasing `main` or shared branches | Teammates' history breaks | Only rebase your private branches |
| Force pushing to `main` | Overwrites history for everyone | Enable branch protection on GitHub |
| Letting branches live for weeks | Massive merge conflicts later | Merge/close branches within days |
| Not pulling before branching | Branch starts from stale point | `git pull` before creating branches |
