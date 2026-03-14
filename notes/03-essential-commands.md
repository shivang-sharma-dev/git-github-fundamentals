# 03 — Essential Commands

> Commands grouped by what you're trying to do, not alphabetically. Each command includes what it does, syntax, real examples, and common mistakes.

---

## Starting Out

### `git init`
Initializes a new Git repository in the current directory. Creates the `.git` folder.

```bash
# In an existing project folder
cd my-project
git init

# Create a new folder AND initialize
git init my-project
cd my-project
```

**Common mistake:** Running `git init` inside your home directory (`~`) or Downloads folder by accident. Always `cd` into your project first.

---

### `git clone`
Copies an existing repository (and its full history) to your local machine.

```bash
# Clone via SSH (recommended)
git clone git@github.com:username/repo.git

# Clone via HTTPS
git clone https://github.com/username/repo.git

# Clone into a custom folder name
git clone git@github.com:username/repo.git my-custom-name

# Clone a specific branch only
git clone -b develop git@github.com:username/repo.git

# Shallow clone (only latest history — faster for big repos)
git clone --depth 1 git@github.com:username/repo.git
```

---

## Daily Workflow

### `git status`
Shows the current state of your working directory and staging area. **Run this constantly** — it's your compass.

```bash
git status

# Shorter output (one line per file)
git status -s
```

Output guide:
```
On branch main
Changes to be committed:          ← in staging area (green)
  modified:   README.md

Changes not staged for commit:    ← modified but not staged (red)
  modified:   app.py

Untracked files:                  ← Git doesn't know about these yet
  new-file.txt
```

---

### `git add`
Moves changes from the working directory to the staging area.

```bash
# Stage a specific file
git add README.md

# Stage multiple files
git add README.md app.py config.yml

# Stage all changes in current directory (and subdirectories)
git add .

# Stage all changes in the whole repo (from any subfolder)
git add -A

# Stage parts of a file interactively (choose specific lines/chunks)
git add -p README.md
```

**`git add .` vs `git add -A`:**
- `git add .` — stages everything in the current directory
- `git add -A` — stages everything including deletions and renames everywhere in the repo

**Common mistake:** Using `git add .` from a subdirectory and missing files in other directories. Use `git add -A` when you want to capture everything.

---

### `git commit`
Saves the staged changes as a permanent commit in the repository.

```bash
# Opens your configured editor to write a message
git commit

# Commit with an inline message (for short messages)
git commit -m "feat: add user authentication"

# Stage all tracked files AND commit in one step (skips git add for tracked files)
git commit -am "fix: correct typo in README"

# Amend the last commit (change message OR add forgotten files)
# Only do this BEFORE pushing to remote
git commit --amend -m "corrected commit message"
git commit --amend --no-edit   # keep the same message, just add staged changes
```

**What makes a good commit message:**
```
feat: add login endpoint for user authentication

- Validates email and password fields
- Returns JWT token on success
- Returns 401 on invalid credentials

Closes #42
```

Structure: `type: short summary (max 72 chars)` then a blank line then details.

Common types: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `style`

---

### `git log`
Shows the commit history.

```bash
# Full log
git log

# One line per commit (most useful for quick overview)
git log --oneline

# With a branch graph
git log --oneline --graph --all

# Last N commits
git log -5

# Commits by a specific author
git log --author="Alice"

# Commits that changed a specific file
git log -- README.md

# Commits between two dates
git log --after="2024-01-01" --before="2024-06-01"

# Search commit messages
git log --grep="authentication"

# See what changed in each commit (patch log)
git log -p
```

**Most useful combination:**
```bash
git log --oneline --graph --all --decorate
```

---

### `git diff`
Shows what changed between versions.

```bash
# Changes in working directory NOT yet staged
git diff

# Changes that ARE staged (ready to commit)
git diff --staged
# (also works as: git diff --cached)

# Changes between two commits
git diff a1b2c3d e4f5a6b

# Changes between two branches
git diff main feature-branch

# Changes in a specific file only
git diff README.md

# Summary of what files changed (no line-by-line)
git diff --stat
```

**Remember:** `git diff` alone shows UNSTAGED changes. `git diff --staged` shows STAGED changes. People forget `--staged` and wonder why they see nothing.

---

### `git show`
Shows details about a specific commit — what changed and the metadata.

```bash
# Show the latest commit
git show

# Show a specific commit
git show a1b2c3d

# Show only the files changed (no line diffs)
git show --stat a1b2c3d

# Show a specific file at a specific commit
git show a1b2c3d:path/to/file.py
```

---

## Undoing Things

This is where most people feel lost. Here's the full map:

| Situation | Command |
|---|---|
| Discard unstaged changes in a file | `git restore filename` |
| Unstage a file (keep changes in working dir) | `git restore --staged filename` |
| Undo last commit, keep changes staged | `git reset --soft HEAD~1` |
| Undo last commit, keep changes unstaged | `git reset --mixed HEAD~1` |
| Undo last commit AND discard all changes | `git reset --hard HEAD~1` |
| Undo a commit that's already been pushed | `git revert a1b2c3d` |

### `git restore`
```bash
# Discard changes in working directory (bring back last committed version)
git restore README.md

# Discard ALL unstaged changes
git restore .

# Unstage a file (move it out of staging area, keep changes)
git restore --staged README.md

# Restore a file to a specific commit's version
git restore --source a1b2c3d README.md
```

### `git reset`

**`--soft`** — moves HEAD back, keeps changes staged
```bash
git reset --soft HEAD~1   # undo last commit, files still staged
git reset --soft HEAD~3   # undo last 3 commits
```

**`--mixed`** (default) — moves HEAD back, unstages changes
```bash
git reset HEAD~1           # same as --mixed
git reset --mixed HEAD~1   # undo last commit, files back to working dir
```

**`--hard`** — moves HEAD back, DELETES changes. Be careful.
```bash
git reset --hard HEAD~1    # undo last commit AND lose all those changes
git reset --hard origin/main  # reset local branch to match remote
```

### `git revert`
Creates a NEW commit that undoes a previous commit. Safe for shared branches because it doesn't rewrite history.

```bash
# Revert a specific commit
git revert a1b2c3d

# Revert without immediately committing (lets you edit the message)
git revert --no-commit a1b2c3d
```

**Reset vs Revert:**
- `reset` — rewrites history (dangerous on shared branches)
- `revert` — adds a new undo commit (safe on shared branches)

**Rule:** Use `reset` on local/private branches. Use `revert` on branches others are using.

---

## Managing Files

### `git rm`
Removes a file from both the working directory AND the staging area.

```bash
# Remove a file (deletes from disk too)
git rm old-file.txt

# Remove from Git tracking but keep the file on disk
git rm --cached secret-config.yml   # useful when you accidentally committed a file
```

### `git mv`
Renames or moves a file (Git tracks the rename properly).

```bash
git mv old-name.py new-name.py
git mv app.py src/app.py
```

You could also use `mv` (regular shell rename) and then `git add -A`, but `git mv` is cleaner.

---

## The `.gitignore` File

Tells Git which files/folders to never track. Lives in the root of your repo.

```bash
# Create it
touch .gitignore
```

### Common patterns

```gitignore
# Ignore a specific file
secret.env
config/database.yml

# Ignore all files with an extension
*.log
*.tmp
*.pyc

# Ignore a folder and everything inside it
node_modules/
__pycache__/
.venv/
dist/
build/

# Ignore files in any subdirectory
**/*.log

# Ignore everything in a folder EXCEPT one file
logs/*
!logs/.gitkeep

# Ignore a file only in the root, not in subdirectories
/TODO

# Comments start with #
# Blank lines are ignored
```

### Global gitignore (for files you never want tracked across ALL repos)
```bash
# Create a global ignore file
touch ~/.gitignore_global

# Tell Git to use it
git config --global core.excludesfile ~/.gitignore_global
```

Put OS-specific files here:
```gitignore
# macOS
.DS_Store
.AppleDouble

# Windows
Thumbs.db
Desktop.ini

# VS Code
.vscode/

# JetBrains IDEs
.idea/
```

**`.gitignore` doesn't work on already-tracked files.** If you already committed a file and now want to ignore it:
```bash
git rm --cached filename
echo "filename" >> .gitignore
git commit -m "chore: stop tracking filename"
```

---

## Stashing — Saving Work Temporarily

Stash saves your uncommitted changes to a temporary storage so you can switch context (e.g., switch branches) and come back later.

```bash
# Save current changes to stash (working dir becomes clean)
git stash

# Save with a descriptive message
git stash save "WIP: half-done login feature"

# List all stashes
git stash list
# Output: stash@{0}: WIP: half-done login feature
#         stash@{1}: On main: quick experiment

# Apply the most recent stash (keeps it in stash list)
git stash apply

# Apply AND remove from stash list
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Delete a specific stash
git stash drop stash@{0}

# Delete all stashes
git stash clear

# See what's in a stash without applying
git stash show -p stash@{0}

# Create a branch from a stash (useful when stash conflicts with current branch)
git stash branch new-feature-branch stash@{0}
```

**Common workflow:**
```bash
# You're mid-work and need to urgently fix a bug on main
git stash save "WIP: feature login"
git checkout main
# ... fix the bug, commit ...
git checkout feature-branch
git stash pop
# Continue where you left off
```

---

## Tags — Marking Important Points in History

Tags mark specific commits as important milestones (typically releases).

```bash
# Create a lightweight tag (just a name, no extra info)
git tag v1.0.0

# Create an annotated tag (has message, author, date — recommended for releases)
git tag -a v1.0.0 -m "First stable release"

# Tag a past commit
git tag -a v0.9.0 a1b2c3d -m "Beta release"

# List all tags
git tag
git tag -l "v1.*"    # filter by pattern

# See tag details
git show v1.0.0

# Push a specific tag to remote
git push origin v1.0.0

# Push ALL tags to remote
git push origin --tags

# Delete a local tag
git tag -d v1.0.0

# Delete a remote tag
git push origin --delete v1.0.0
```

**Lightweight vs Annotated:**
- Lightweight = just a pointer to a commit. Like a branch that doesn't move.
- Annotated = a full object with its own metadata. Use for releases.

---

## Useful Utility Commands

```bash
# See who last changed each line of a file
git blame README.md

# Search through all committed code
git grep "TODO"
git grep -n "function login"   # -n shows line numbers

# Find which commit introduced a bug (binary search)
git bisect start
git bisect bad                  # current version has the bug
git bisect good v1.0.0          # this version was fine
# Git checks out midpoints — you test each and mark good/bad
git bisect good    # or: git bisect bad
# ... repeat until Git finds the culprit commit
git bisect reset   # return to HEAD when done

# See a summary of changes (files and number of lines changed)
git diff --stat HEAD~5

# Clean up untracked files (dry run first!)
git clean -n    # shows what WOULD be deleted
git clean -fd   # actually delete untracked files and folders
```

---

## Command Quick Reference

```bash
# ── Starting out ──────────────────────────────────────
git init                          # start a new repo
git clone <url>                   # copy a remote repo

# ── Daily ─────────────────────────────────────────────
git status                        # what's going on
git add <file>                    # stage a file
git add .                         # stage everything
git commit -m "message"           # save staged changes
git log --oneline --graph --all   # see history

# ── Comparing ─────────────────────────────────────────
git diff                          # unstaged changes
git diff --staged                 # staged changes
git show <hash>                   # inspect a commit

# ── Undoing ───────────────────────────────────────────
git restore <file>                # discard working dir changes
git restore --staged <file>       # unstage
git reset --soft HEAD~1           # undo commit, keep staged
git reset --hard HEAD~1           # undo commit, lose changes
git revert <hash>                 # safe undo (new commit)

# ── Stashing ──────────────────────────────────────────
git stash                         # save work temporarily
git stash pop                     # bring it back
git stash list                    # see all stashes

# ── Files ─────────────────────────────────────────────
git rm <file>                     # delete and untrack
git rm --cached <file>            # untrack only
git mv <old> <new>                # rename/move

# ── Tags ──────────────────────────────────────────────
git tag -a v1.0.0 -m "release"   # create annotated tag
git push origin --tags            # push all tags
```
