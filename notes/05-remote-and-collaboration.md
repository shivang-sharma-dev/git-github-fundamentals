# 05 — Remote Repositories and Collaboration

> This is where Git goes from a personal tool to a team superpower. Understanding remotes, PRs, and collaboration patterns is essential for every DevOps role.

---

## What is a Remote?

A remote is a version of your repository hosted elsewhere — on GitHub, GitLab, Bitbucket, or even another server. It's where your team shares code.

Remotes have names. The default remote (the one you cloned from) is always called `origin`.

```bash
# See all configured remotes
git remote -v
# origin  git@github.com:username/repo.git (fetch)
# origin  git@github.com:username/repo.git (push)

# Add a new remote
git remote add origin git@github.com:username/repo.git

# Add a second remote (common when you've forked a repo)
git remote add upstream git@github.com:original-owner/repo.git

# Rename a remote
git remote rename origin old-origin

# Remove a remote
git remote remove upstream

# Change a remote's URL (e.g., switching from HTTPS to SSH)
git remote set-url origin git@github.com:username/repo.git
```

---

## Fetch, Pull, and Push

### `git fetch`
Downloads new data from the remote but does **NOT** change your local working files or branches.

```bash
# Fetch all changes from origin
git fetch origin

# Fetch a specific branch
git fetch origin main

# Fetch from all remotes
git fetch --all

# Fetch and clean up deleted remote branches
git fetch --prune
```

After fetching, you can inspect what changed before integrating:
```bash
git log HEAD..origin/main   # what's on remote but not local
git diff HEAD origin/main   # what would change if I merged
```

### `git pull`
Fetches AND merges (or rebases) into your current branch. It's a shortcut for `git fetch` + `git merge`.

```bash
# Pull (fetch + merge)
git pull

# Pull with rebase instead of merge (cleaner history)
git pull --rebase

# Set rebase as default pull behavior
git config --global pull.rebase true

# Pull a specific branch
git pull origin main
```

**`fetch` vs `pull`:**

| | `fetch` | `pull` |
|---|---|---|
| Downloads remote changes | Yes | Yes |
| Modifies local files | No | Yes |
| Risk of conflicts | None | Possible |
| Best for | Reviewing before integrating | Quick sync when you trust the changes |

**Prefer `fetch` first, then merge/rebase** when working on shared repos. It gives you a chance to see what you're getting before it lands in your code.

---

### `git push`
Uploads your local commits to the remote.

```bash
# Push current branch to origin (matching branch name)
git push

# Push a specific branch
git push origin feature-login

# Push and set upstream (first push of a new branch)
git push -u origin feature-login
# After -u, you can just use 'git push' for this branch

# Push all local branches
git push --all

# Push tags (not pushed by default)
git push origin --tags
git push origin v1.0.0   # push a specific tag

# Delete a remote branch
git push origin --delete feature-old
```

### Force Push — Use Carefully

```bash
# Force push (DANGEROUS — overwrites remote history)
git push --force

# Safer alternative: fails if remote has commits you don't have locally
git push --force-with-lease
```

**When is force push OK?**
- Your own private feature branch that no one else has pulled
- After an interactive rebase to clean up commits before a PR

**When is force push NOT OK?**
- main, develop, or any shared branch — ever

**Always use `--force-with-lease` over `--force`** — it protects you from overwriting someone else's commits on the same branch.

---

## Tracking Branches

When a local branch is linked to a remote branch, it's called a "tracking branch." This is what lets `git push` and `git pull` know where to send/receive.

```bash
# See tracking relationships for all branches
git branch -vv
# main      a1b2c3d [origin/main] last commit message
# feature   b2c3d4e [origin/feature: ahead 2] some commit

# "ahead 2" = you have 2 commits to push
# "behind 3" = remote has 3 commits you need to pull
# "ahead 2, behind 1" = diverged — need to merge or rebase

# Set tracking for existing branch
git branch -u origin/feature-login feature-login
```

---

## The Fork Workflow

Forking is used when you don't have write access to a repository — common in open source. You fork (copy) the repo to your account, make changes, then submit a Pull Request.

```
Original repo (you can't push here)
        │
        ├── you fork it on GitHub
        ▼
Your fork (you control this)
        │
        ├── git clone (to your machine)
        ▼
Your local clone
        │
        ├── create branch, make changes, push to YOUR fork
        ▼
Pull Request: your fork → original repo
```

### Setup for fork workflow

```bash
# 1. Fork on GitHub (click Fork button)

# 2. Clone YOUR fork
git clone git@github.com:YOUR-USERNAME/repo.git
cd repo

# 3. Add original repo as 'upstream'
git remote add upstream git@github.com:ORIGINAL-OWNER/repo.git

# 4. Verify
git remote -v
# origin    git@github.com:YOUR-USERNAME/repo.git (fetch)
# origin    git@github.com:YOUR-USERNAME/repo.git (push)
# upstream  git@github.com:ORIGINAL-OWNER/repo.git (fetch)
# upstream  git@github.com:ORIGINAL-OWNER/repo.git (push)
```

### Keeping your fork up to date

```bash
# Fetch latest changes from original repo
git fetch upstream

# Merge upstream main into your local main
git switch main
git merge upstream/main

# Push the updated main to your fork
git push origin main
```

Do this regularly to avoid large merge conflicts when your PR is reviewed.

---

## Pull Requests (PRs)

A Pull Request is a GitHub/GitLab feature (not a Git feature) that:
1. Notifies teammates of your changes
2. Shows a diff of all changes for review
3. Allows comments, discussions, and approvals
4. Merges changes into the target branch when approved

### The PR Lifecycle

```
1. Create feature branch locally
         ↓
2. Make commits, push branch to remote
         ↓
3. Open Pull Request on GitHub
         ↓
4. Teammates review — leave comments
         ↓
5. You address feedback, push more commits
         ↓
6. Approvals received (usually 1-2 reviewers required)
         ↓
7. Merge PR (squash, merge commit, or rebase)
         ↓
8. Delete feature branch
```

### Writing a good PR description

A PR description should tell reviewers:
- **What** changed (brief summary)
- **Why** (the problem being solved or feature being added)
- **How** (approach taken, any design decisions)
- **Testing** (how was it verified)
- Links to related issues

```markdown
## Summary
Adds user authentication with JWT tokens.

## Changes
- New `/login` endpoint validates credentials and returns a JWT
- New `/logout` endpoint invalidates the token
- Added middleware for protected routes

## Why
Users currently can access all pages without logging in. This adds
security before the public launch.

## Testing
- Unit tests added for auth middleware
- Manual testing with Postman (screenshots attached)

Closes #42
```

### PR review etiquette

**As an author:**
- Keep PRs small and focused (one feature/fix per PR)
- Self-review before requesting others
- Respond to all comments (even with "done" or "acknowledged")
- Don't take feedback personally

**As a reviewer:**
- Be specific and actionable in feedback
- Distinguish between blocking issues vs nice-to-haves
- Approve promptly — don't let PRs sit for days
- Ask questions, don't just demand changes

---

## Branch Protection Rules

On GitHub, you can protect important branches from direct pushes and bad practices.

**Settings → Branches → Add rule** for `main`:

Recommended settings:
- **Require a pull request before merging** — no direct commits to main
- **Require N approvals** — at least 1-2 reviewers must approve
- **Require status checks to pass** — CI/CD must be green before merging
- **Require branches to be up to date** — branch must be current with main
- **Restrict who can push** — only specific teams/people
- **Do not allow force pushes** — protects history
- **Do not allow deletions** — can't delete the branch

```bash
# With branch protection, this will be rejected:
git push --force origin main    # Error: remote rejected

# Even admins should use PRs for main
```

---

## Syncing with Remote — Common Scenarios

### You're behind remote (someone else pushed)
```bash
git fetch origin
git merge origin/main
# OR in one step:
git pull origin main
```

### You and remote have diverged (you both committed)
```bash
git fetch origin
git rebase origin/main   # replay your commits on top of theirs
# OR
git merge origin/main    # merge their changes into yours
git push origin main
```

### Your local branch is ahead of remote
```bash
git push origin feature-login   # simply push your commits
```

### Remote branch was deleted but shows locally
```bash
git fetch --prune   # cleans up stale remote-tracking branches
git branch -r       # should no longer show the deleted branch
```

---

## GitHub-Specific Features

### Linking commits to issues
```bash
# These keywords in commit messages automatically close issues on merge:
git commit -m "fix: correct login redirect — fixes #42"
git commit -m "feat: add dark mode — closes #67, closes #68"
# Keywords: closes, fixes, resolves (case-insensitive)
```

### Referencing other issues/PRs
```
In a PR description or comment:
#42        — links to issue/PR #42 in the same repo
owner/repo#42  — links to another repo's issue
```

### GitHub Actions (overview)
```yaml
# .github/workflows/ci.yml
# This runs automatically on every push and pull request
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm test
```

PRs will show a green checkmark (or red X) from these automated checks. Branch protection can require the checks to pass before merging.

---

## Remote Command Quick Reference

```bash
# ── Remotes ───────────────────────────────────────────
git remote -v                          # list remotes
git remote add origin <url>            # add remote
git remote add upstream <url>          # add upstream (forks)
git remote set-url origin <url>        # change remote URL

# ── Getting changes ───────────────────────────────────
git fetch origin                       # download, don't merge
git fetch --all --prune                # fetch all, clean stale
git pull                               # fetch + merge
git pull --rebase                      # fetch + rebase

# ── Sending changes ───────────────────────────────────
git push                               # push current branch
git push -u origin feature-login       # push + set upstream
git push --force-with-lease            # safe force push
git push origin --delete old-branch    # delete remote branch

# ── Inspecting ────────────────────────────────────────
git branch -vv                         # see tracking info
git log HEAD..origin/main              # what's on remote
git diff HEAD origin/main              # diff vs remote

# ── Syncing fork ──────────────────────────────────────
git fetch upstream
git merge upstream/main
git push origin main
```

---

## Common Collaboration Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Pushing directly to `main` | Skips review, breaks CI | Use PRs — enable branch protection |
| Opening a PR with 50+ commits | Impossible to review | Keep PRs small, squash before opening |
| Not syncing fork before PR | Merge conflicts when submitting | `git fetch upstream` regularly |
| Force pushing a shared branch | Breaks teammates' history | Never force push shared branches |
| Ignoring PR review comments | PR sits unmerged indefinitely | Respond to every comment |
| Merging before CI passes | Broken code goes to main | Wait for green status checks |
