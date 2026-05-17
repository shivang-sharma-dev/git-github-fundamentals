# 🐙 Git & GitHub Daily Cheatsheet — All-in-One Reference

> **Pro Tip:** Use `git <command> --help` for full documentation on any command.

---

## 📌 Quick Reference Card

| Task | Command |
|---|---|
| Initialize repo | `git init` |
| Clone a repo | `git clone <url>` |
| Check status | `git status` |
| Stage files | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push origin main` |
| Pull | `git pull origin main` |
| Create branch | `git checkout -b feature` |
| Switch branch | `git switch main` |
| Merge branch | `git merge feature` |
| View log | `git log --oneline` |
| Undo last commit | `git reset --soft HEAD~1` |
| Stash changes | `git stash` |
| View remotes | `git remote -v` |

---

## ⚙️ 1. SETUP & CONFIGURATION

```bash
# Identity (set once globally)
git config --global user.name "Shivang Sharma"
git config --global user.email "shivang@example.com"

# Default branch name
git config --global init.defaultBranch main

# Default editor
git config --global core.editor "code --wait"    # VS Code
git config --global core.editor "vim"            # Vim
git config --global core.editor "nano"           # Nano

# Line endings (important for cross-platform)
git config --global core.autocrlf input          # Linux/Mac
git config --global core.autocrlf true           # Windows

# Colorful output
git config --global color.ui auto

# Set rebase as default pull strategy
git config --global pull.rebase true

# View all config
git config --list
git config --global --list
git config user.name                             # View single value

# Config levels
# --system  → /etc/gitconfig (all users)
# --global  → ~/.gitconfig (current user)
# --local   → .git/config (current repo)

# Aliases (optional — covered in section 17)
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.lg "log --oneline --graph --all"
```

---

## 🏁 2. INIT & CLONE

```bash
# Initialize a new repo
git init                             # In current directory
git init my-project/                 # Create new directory
git init --bare repo.git             # Bare repo (for servers)

# Clone
git clone <url>                              # Clone into folder named from repo
git clone <url> my-folder                   # Clone into custom folder
git clone <url> .                           # Clone into current directory
git clone --depth=1 <url>                   # Shallow clone (latest commit only)
git clone --branch develop <url>            # Clone specific branch
git clone --recursive <url>                 # Clone with submodules

# GitHub SSH vs HTTPS
git clone https://github.com/user/repo.git  # HTTPS (needs token/password)
git clone git@github.com:user/repo.git      # SSH (needs SSH key setup)
```

---

## 📸 3. STAGING — Add Files

```bash
# Stage files
git add file.txt                     # Stage specific file
git add .                            # Stage ALL changes in current dir
git add -A                           # Stage all (including deletions)
git add *.py                         # Stage all Python files
git add src/                         # Stage entire directory

# Stage interactively (choose hunks)
git add -p                           # Patch mode — approve chunks one by one
git add -p file.txt                  # Patch specific file

# View staged vs unstaged
git status                           # Overview
git diff                             # Unstaged changes
git diff --staged                    # Staged changes (what will be committed)
git diff HEAD                        # All changes vs last commit

# Unstage files (without losing changes)
git restore --staged file.txt        # Unstage specific file
git restore --staged .               # Unstage everything
git reset HEAD file.txt              # Older alternative
```

---

## 💾 4. COMMIT

```bash
# Basic commit
git commit -m "feat: add user authentication"

# Stage + commit tracked files in one step
git commit -am "fix: correct typo in settings"

# Open editor for long commit message
git commit

# Amend last commit (fix message or add forgotten file)
git add forgotten-file.txt
git commit --amend -m "corrected message"
git commit --amend --no-edit            # Amend without changing message

# Empty commit (useful to trigger CI/CD)
git commit --allow-empty -m "chore: trigger pipeline"

# Commit with author override
git commit -m "message" --author="Name <email>"

# Conventional Commits Format (best practice)
# feat:     New feature
# fix:      Bug fix
# docs:     Documentation only
# style:    Formatting, no logic change
# refactor: Code restructure, no feature/fix
# test:     Adding tests
# chore:    Maintenance (build, deps, CI)
# perf:     Performance improvement
# ci:       CI/CD changes

# Examples:
git commit -m "feat: add notes list API endpoint"
git commit -m "fix: resolve port-forward connection error"
git commit -m "chore: update k8s deployment manifest"
git commit -m "docs: add Linux cheatsheet"
```

---

## 🌿 5. BRANCHING

```bash
# List branches
git branch                           # Local branches
git branch -r                        # Remote branches
git branch -a                        # All (local + remote)
git branch -v                        # With last commit info

# Create branch
git branch feature-login             # Create (stays on current branch)
git checkout -b feature-login        # Create + switch (classic)
git switch -c feature-login          # Create + switch (modern, preferred)

# Switch branch
git checkout main                    # Classic
git switch main                      # Modern (preferred)
git switch -                         # Switch to previous branch

# Rename branch
git branch -m old-name new-name      # Rename local branch
git branch -m new-name               # Rename current branch

# Delete branch
git branch -d feature-login          # Safe delete (must be merged)
git branch -D feature-login          # Force delete (even if unmerged)
git push origin --delete feature-login   # Delete remote branch

# Track a remote branch
git checkout --track origin/feature  # Create local branch tracking remote
git branch -u origin/main main       # Set upstream for existing branch

# See tracking info
git branch -vv
```

---

## 🔀 6. MERGE

```bash
# Merge a branch into current branch
git merge feature-login              # Merge with merge commit
git merge --no-ff feature-login      # Always create merge commit (even if fast-forward possible)
git merge --squash feature-login     # Squash all commits into one (then commit manually)
git merge --abort                    # Abort a merge in progress

# Resolve merge conflicts workflow:
# 1. git merge feature
# 2. Conflict appears in files — edit them
# 3. git add <resolved-file>
# 4. git commit

# View conflicts
git status                           # Shows conflicted files
git diff                             # Shows conflict markers

# Accept one side entirely
git checkout --ours file.txt         # Keep current branch version
git checkout --theirs file.txt       # Keep incoming branch version

# Merge specific commit from another branch (cherry-pick)
git cherry-pick <commit-hash>        # Apply a single commit to current branch
git cherry-pick abc123 def456        # Multiple commits
git cherry-pick --no-commit abc123   # Apply without committing
git cherry-pick --abort              # Abort cherry-pick
```

---

## 🔄 7. REBASE

```bash
# Rebase current branch onto main
git rebase main

# Interactive rebase — edit, squash, reorder commits
git rebase -i HEAD~3                 # Last 3 commits
git rebase -i HEAD~5                 # Last 5 commits
# In editor, pick options:
# pick   → keep commit as is
# reword → edit commit message
# edit   → stop and amend the commit
# squash → combine with previous commit
# fixup  → squash but discard message
# drop   → delete the commit

# Rebase onto a specific commit
git rebase --onto main feature~3 feature

# Rebase workflow (clean up before PR):
git switch feature-login
git rebase main                      # Bring in latest main changes
git push --force-with-lease          # Force push rebased branch

# Abort / continue / skip
git rebase --abort
git rebase --continue                # After resolving conflict
git rebase --skip                    # Skip a conflicted commit

# ⚠️ GOLDEN RULE: Never rebase shared/public branches
# Only rebase your own local / feature branches
```

---

## 📦 8. STASH — Save Work in Progress

```bash
# Stash current changes (tracked files)
git stash                            # Quick stash
git stash save "WIP: login feature"  # Named stash

# Include untracked files
git stash -u
git stash --include-untracked

# List stashes
git stash list
# stash@{0}: WIP on main: abc123 last commit message
# stash@{1}: WIP: login feature

# Apply stash
git stash pop                        # Apply latest + remove from stash list
git stash apply                      # Apply latest + KEEP in stash list
git stash apply stash@{1}           # Apply specific stash

# Inspect stash contents
git stash show                       # Summary of latest stash
git stash show -p                    # Full diff of latest stash
git stash show stash@{1}

# Delete stashes
git stash drop stash@{0}            # Delete specific stash
git stash clear                      # Delete ALL stashes ⚠️

# Create branch from stash (great for big WIP)
git stash branch feature-new stash@{0}
```

---

## 🌍 9. REMOTES

```bash
# View remotes
git remote -v                        # Show remote URLs
git remote show origin               # Detailed remote info

# Add / remove remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git
git remote remove origin
git remote rename origin upstream

# Change remote URL (e.g., switch HTTPS to SSH)
git remote set-url origin git@github.com:user/repo.git

# Fetch — download changes without merging
git fetch origin                     # Fetch all branches from origin
git fetch --all                      # Fetch from all remotes
git fetch origin main                # Fetch specific branch

# Pull — fetch + merge (or fetch + rebase)
git pull origin main
git pull --rebase origin main        # Pull with rebase instead of merge
git pull --all                       # Pull all tracked branches

# Push
git push origin main                 # Push main to origin
git push origin feature-login        # Push feature branch
git push -u origin feature-login     # Push + set upstream (first time)
git push --all origin                # Push all branches
git push --tags                      # Push all tags
git push --force-with-lease          # Safe force push (won't overwrite others' work)
git push --force                     # Force push ⚠️ use with caution
git push origin --delete old-branch  # Delete remote branch

# Sync fork with upstream
git fetch upstream
git merge upstream/main
# or
git rebase upstream/main
git push origin main
```

---

## 🔎 10. LOG & HISTORY

```bash
# Basic log
git log                              # Full log
git log --oneline                    # Compact one-line per commit
git log --oneline --graph            # ASCII branch graph
git log --oneline --graph --all      # All branches in graph
git log --oneline -10                # Last 10 commits

# Filter log
git log --author="Shivang"          # By author
git log --since="2026-01-01"        # Since date
git log --since="2 weeks ago"
git log --until="2026-05-01"
git log --grep="fix"                 # Commits with "fix" in message
git log -- file.txt                  # Commits that touched a file
git log main..feature                # Commits in feature not in main
git log --merges                     # Only merge commits
git log --no-merges                  # Exclude merge commits

# Detailed log
git log -p                           # Full diff for each commit
git log -p file.txt                  # Diff history for a file
git log --stat                       # Files changed per commit
git log --shortstat                  # Shorter stats

# Find who changed a line (blame)
git blame file.txt                   # Who wrote each line
git blame -L 10,20 file.txt          # Lines 10–20 only

# Search for a string in history
git log -S "DB_HOST"                 # Find commits that added/removed "DB_HOST"
git log -G "regex"                   # Search with regex

# Show a specific commit
git show abc1234
git show abc1234:file.txt            # View file at that commit

# Reflog — your safety net (ALL HEAD movements)
git reflog                           # Every place HEAD has been
git reflog --all                     # All refs
```

---

## ↩️ 11. UNDOING MISTAKES

```bash
# ── BEFORE STAGING ──────────────────────────────────────
# Discard changes in working directory (PERMANENT)
git restore file.txt                 # Discard unstaged changes in file
git restore .                        # Discard ALL unstaged changes ⚠️
git checkout -- file.txt             # Older equivalent

# ── AFTER STAGING ───────────────────────────────────────
# Unstage (keep changes in working directory)
git restore --staged file.txt
git reset HEAD file.txt              # Older equivalent

# ── AFTER COMMITTING ────────────────────────────────────
# Undo last commit — keep changes STAGED
git reset --soft HEAD~1

# Undo last commit — keep changes UNSTAGED
git reset --mixed HEAD~1             # (default reset behavior)

# Undo last commit — DISCARD all changes ⚠️
git reset --hard HEAD~1

# Undo multiple commits
git reset --soft HEAD~3              # Undo last 3 commits, keep staged

# ── SAFE UNDO (creates a new commit) ────────────────────
# Revert a commit (safe for shared/public branches)
git revert abc1234                   # Create new commit that undoes abc1234
git revert HEAD                      # Revert latest commit
git revert HEAD~2..HEAD              # Revert last 2 commits

# ── RECOVERY ────────────────────────────────────────────
# Recover a deleted branch or commit
git reflog                           # Find the lost commit hash
git checkout -b recovered abc1234   # Restore it as a new branch

# Restore a deleted file
git checkout HEAD -- deleted-file.txt

# ── RESET CHEAT SHEET ───────────────────────────────────
# --soft  → repo ✅  stage ✅  working dir ✅  (only moves HEAD)
# --mixed → repo ✅  stage ❌  working dir ✅  (default)
# --hard  → repo ✅  stage ❌  working dir ❌  (everything lost)
```

---

## 🏷️ 12. TAGS — Versioning

```bash
# Create tags
git tag v1.0.0                       # Lightweight tag
git tag -a v1.0.0 -m "Release v1.0.0"  # Annotated tag (preferred)
git tag -a v1.0.0 abc1234 -m "msg"  # Tag a specific commit

# List tags
git tag
git tag -l "v1.*"                    # Filter by pattern
git show v1.0.0                      # Show tag details

# Push tags
git push origin v1.0.0               # Push specific tag
git push origin --tags               # Push all tags

# Delete tags
git tag -d v1.0.0                    # Delete local tag
git push origin --delete v1.0.0      # Delete remote tag

# Checkout at a tag (detached HEAD)
git checkout v1.0.0

# Semantic versioning: MAJOR.MINOR.PATCH
# v1.0.0 → v1.0.1 (patch: bug fix)
# v1.0.0 → v1.1.0 (minor: new feature, backward compatible)
# v1.0.0 → v2.0.0 (major: breaking change)
```

---

## 📋 13. .gitignore

```bash
# Create .gitignore
touch .gitignore

# Ignore already-tracked files (must untrack first)
git rm --cached file.txt             # Remove from tracking, keep on disk
git rm --cached -r node_modules/     # Remove directory from tracking
git rm -r --cached .                 # Remove all tracking, re-add after

# Check why a file is ignored
git check-ignore -v filename

# Global gitignore (for editor/OS files)
git config --global core.excludesfile ~/.gitignore_global
```

```gitignore
# Python / Django
__pycache__/
*.py[cod]
*.pyo
.env
.venv/
venv/
*.egg-info/
db.sqlite3
*.log

# Node.js
node_modules/
dist/
build/
.npm
.cache

# Docker
.dockerignore

# Kubernetes
*.kubeconfig

# OS
.DS_Store
Thumbs.db
desktop.ini

# IDE
.vscode/
.idea/
*.swp
*.swo

# Secrets (NEVER commit these)
*.pem
*.key
*.p12
.env*
secrets/
credentials.json
```

---

## 🔗 14. GITHUB — Remote Workflows

```bash
# ── PULL REQUESTS (via GitHub CLI) ──────────────────────
gh pr create --title "feat: add login" --body "Description here"
gh pr create --base main --head feature-login
gh pr list                           # List open PRs
gh pr view 42                        # View PR #42
gh pr checkout 42                    # Check out PR #42 locally
gh pr merge 42 --squash              # Merge PR with squash
gh pr merge 42 --rebase              # Merge PR with rebase
gh pr close 42                       # Close PR without merging

# ── ISSUES ──────────────────────────────────────────────
gh issue create --title "Bug: crash on login" --label bug
gh issue list
gh issue view 10
gh issue close 10

# ── REPOS ───────────────────────────────────────────────
gh repo create my-app --public       # Create public repo
gh repo create my-app --private      # Create private repo
gh repo clone user/repo              # Clone via gh
gh repo fork user/repo               # Fork a repo
gh repo view                         # View current repo in browser

# ── RELEASES ────────────────────────────────────────────
gh release create v1.0.0 --title "v1.0.0" --notes "Release notes"
gh release list
gh release view v1.0.0
gh release delete v1.0.0

# ── GISTS ───────────────────────────────────────────────
gh gist create file.txt              # Create gist from file
gh gist list

# ── AUTH ────────────────────────────────────────────────
gh auth login                        # Authenticate GitHub CLI
gh auth status                       # Check auth status
gh auth logout
```

---

## 🔑 15. SSH KEY SETUP FOR GITHUB

```bash
# 1. Generate SSH key
ssh-keygen -t ed25519 -C "shivang@example.com"
# Save to: ~/.ssh/id_ed25519  (press Enter for default)

# 2. Start ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 3. Copy public key
cat ~/.ssh/id_ed25519.pub
# Paste this into: GitHub → Settings → SSH Keys → New SSH Key

# 4. Test connection
ssh -T git@github.com
# Expected: "Hi username! You've successfully authenticated"

# 5. Switch existing repo from HTTPS to SSH
git remote set-url origin git@github.com:user/repo.git
git remote -v                        # Verify

# Multiple GitHub accounts
# ~/.ssh/config:
# Host github-work
#     HostName github.com
#     User git
#     IdentityFile ~/.ssh/id_ed25519_work
#
# Host github-personal
#     HostName github.com
#     User git
#     IdentityFile ~/.ssh/id_ed25519
```

---

## 🤖 16. GITHUB ACTIONS — CI/CD Quick Reference

```yaml
# .github/workflows/ci.yml

name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: python manage.py test

  docker:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push image
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: user/repo:${{ github.sha }}
```

```bash
# Trigger workflow manually
gh workflow run ci.yml

# List workflow runs
gh run list

# View a run
gh run view <run-id>

# Watch run live
gh run watch <run-id>
```

---

## 🔁 17. COMMON WORKFLOWS

```bash
# ── FEATURE BRANCH WORKFLOW ─────────────────────────────
git switch main
git pull origin main                   # Get latest
git switch -c feature/user-auth        # Create feature branch
# ... make changes ...
git add .
git commit -m "feat: add user auth"
git push -u origin feature/user-auth   # Push branch
# Open PR on GitHub → Review → Merge

# ── KEEP FEATURE BRANCH UP TO DATE ──────────────────────
git switch feature/user-auth
git fetch origin
git rebase origin/main                 # Bring in main's latest changes
git push --force-with-lease            # Update remote feature branch

# ── SQUASH COMMITS BEFORE MERGE ─────────────────────────
git rebase -i origin/main              # Squash all feature commits

# ── HOTFIX WORKFLOW ──────────────────────────────────────
git switch main
git pull origin main
git switch -c hotfix/critical-bug
# ... fix it ...
git commit -m "fix: resolve critical crash"
git switch main
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1 -m "Hotfix v1.0.1"
git push origin main --tags
git branch -d hotfix/critical-bug

# ── FORK & CONTRIBUTE WORKFLOW ───────────────────────────
# 1. Fork repo on GitHub
# 2. Clone your fork
git clone git@github.com:yourname/repo.git
# 3. Add upstream
git remote add upstream git@github.com:original/repo.git
# 4. Create feature branch & work
git switch -c my-feature
# 5. Sync with upstream before PR
git fetch upstream
git rebase upstream/main
# 6. Push to your fork
git push origin my-feature
# 7. Open PR from your fork to original
```

---

## ⚡ 18. ALIASES (Add to ~/.bashrc or ~/.zshrc)

```bash
alias g='git'
alias gs='git status'
alias ga='git add .'
alias gc='git commit -m'
alias gca='git commit -am'
alias gp='git push'
alias gpl='git pull'
alias gf='git fetch --all'
alias gb='git branch'
alias gba='git branch -a'
alias gsw='git switch'
alias gl='git log --oneline --graph --all'
alias glo='git log --oneline -15'
alias gd='git diff'
alias gds='git diff --staged'
alias grb='git rebase'
alias grbi='git rebase -i'
alias gst='git stash'
alias gstp='git stash pop'
alias grt='git restore'
alias grts='git restore --staged'

# Usage:
# gs            → git status
# gc "message"  → git commit -m "message"
# gsw main      → git switch main
# gl            → pretty log graph
```

---

## 🔥 Common Mistakes & Fixes

| ❌ Mistake | ✅ Fix |
|---|---|
| `git push --force` on main | Use `--force-with-lease`, never force push shared branches |
| Committing secrets/passwords | Use `.gitignore`, rotate credentials immediately |
| Huge commit with everything | Commit small and often, use `git add -p` |
| Forgetting to pull before push | Always `git pull --rebase` before pushing |
| Rebasing public/shared branches | Only rebase local/feature branches |
| Bad commit message | `git commit --amend` (if not pushed yet) |
| Wrong branch for changes | `git stash` → switch branch → `git stash pop` |
| `git add .` added wrong files | `git restore --staged .` to unstage all |
| Can't find a lost commit | `git reflog` is your best friend |
| Merge conflict panic | `git merge --abort` to start fresh |

---

## 📊 Git Cheat Matrix

```
Working Dir  →  git add  →  Staging Area  →  git commit  →  Local Repo  →  git push  →  Remote
    ↑                              ↑                               ↑                        |
    └── git restore            git restore                   git reset                      |
                                  --staged                                                  |
    ←────────────────────────────── git pull / git fetch + merge ──────────────────────────┘
```

---

*Generated for daily Git & GitHub workflow management · Last updated: May 2026*
