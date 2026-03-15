# Git Flow Diagrams

> Visual reference for branching flows, commit graphs, and merge strategies.

---

## The Three Areas

```
┌──────────────────┐       git add        ┌──────────────────┐      git commit      ┌──────────────────┐
│                  │ ──────────────────►  │                  │ ──────────────────►  │                  │
│  Working         │                      │  Staging Area    │                      │  Repository      │
│  Directory       │ ◄──────────────────  │  (Index)         │                      │  (.git folder)   │
│                  │    git restore        │                  │                      │                  │
└──────────────────┘                      └──────────────────┘                      └──────────────────┘
  Your files on disk                       Next commit draft                          Permanent history
```

---

## File Lifecycle

```
  New file
     │
     ▼
 Untracked ──── git add ────────────► Staged ──── git commit ────► Unmodified
     ▲                                                                   │
     │                                                             (edit the file)
     │                                                                   │
     └──── git rm ────────────────────────────────────────────── Modified
                                                                         │
                                                               git add ──► Staged
```

---

## Linear Commit History

```
 A ── B ── C ── D ── E
                          ↑
                        HEAD (main)
```

---

## Fast-Forward Merge

```
BEFORE:
  main:     A ── B ── C
                       \
  feature:              D ── E ── F
                                   ↑
                                 HEAD

AFTER (git merge feature):
  main:     A ── B ── C ── D ── E ── F
                                       ↑
                               HEAD (main)

  No merge commit created — pointer just moves forward.
```

---

## 3-Way Merge

```
BEFORE:
  main:     A ── B ── C ── D
                  \
  feature:         E ── F ── G

AFTER (git merge feature while on main):
  main:     A ── B ── C ── D ── M
                  \              ↑
  feature:         E ── F ── G ─┘
                                   M = merge commit (has 2 parents: D and G)
```

---

## Rebase

```
BEFORE:
  main:     A ── B ── C ── D
                  \
  feature:         E ── F

AFTER (git rebase main while on feature):
  main:     A ── B ── C ── D
                             \
  feature:                    E' ── F'
                                       E' and F' are NEW commits (same changes, new hashes)
                                       Original E and F no longer referenced

THEN (git merge feature — fast-forward):
  main:     A ── B ── C ── D ── E' ── F'
```

---

## Interactive Rebase — Squashing Commits

```
BEFORE (git rebase -i HEAD~4):
  a1b2c3  feat: start login page
  b2c3d4  fix: typo in button label
  c3d4e5  fix: another typo
  d4e5f6  feat: complete login logic

AFTER (squash b,c,d into a):
  a9z8y7  feat: add complete login page    ← one clean commit
```

---

## Cherry-Pick

```
  main:     A ── B ── C ── D
                  \
  feature:         E ── F ── G
                              ↑
                         (want just this commit on main)

  git switch main
  git cherry-pick G

  main:     A ── B ── C ── D ── G'
                                   G' = copy of G's changes, new hash
```

---

## Git Flow

```
main         ────────────────────────────────────── v1.0 ─────────────────── v1.1
              \                                       ↑  \                    ↑
               \                   merge to main ────┘   \  hotfix ──────────┘
develop         ───────────────────────────────────────────────────────────────
                    \             /           \          /
feature/login        ────────────             \        /
                                    release/1.0────────
```

### Git Flow Branch Rules

```
main        ← production only. tagged with every release.
develop     ← integration. all features merge here first.
feature/*   ← branch from develop, merge back to develop.
release/*   ← branch from develop, merge to main AND develop.
hotfix/*    ← branch from main, merge to main AND develop.
```

---

## GitHub Flow

```
main     ────────────────────── merge ─────────────────── merge ────────
              \                  /         \               /
feature/A      ──────────────────            \             /
                                    feature/B─────────────
```

### GitHub Flow Rules

```
main        ← always deployable. every merge triggers a deployment.
feature/*   ← branch from main, PR back to main. short-lived (days).
```

---

## Trunk-Based Development

```
main (trunk)    ── commit ── commit ── commit ── commit ── commit ──
                                 \              /
feature (1-2 days max)            ──────────────
```

### TBD Rules

```
main        ← everyone commits here. always deployable.
feature/*   ← max 1-2 days old. merged same day via PR.
```

---

## Workflow Comparison

```
Git Flow:         main ← release ← develop ← feature
                  hotfix → main + develop
                  Good for: versioned releases, mobile apps

GitHub Flow:      main ← feature
                  Good for: SaaS, continuous deployment

Trunk-Based:      main ← (very short feature branches)
                  Good for: elite CI/CD teams, Google-scale
```

---

## Detached HEAD State

```
NORMAL (HEAD points to a branch):
  A ── B ── C ── D  ← main ← HEAD

DETACHED HEAD (HEAD points to a commit directly):
  A ── B ── C ── D  ← main
             ↑
           HEAD

  Happened because: git checkout B (by hash, not branch name)
  Fix: git switch -c recovery-branch  (create a branch from here)
       or: git switch main             (go back, losing any new commits)
```

---

## Reset Modes

```
BEFORE:    A ── B ── C ── D  ← HEAD (main)

git reset --soft HEAD~2:
           A ── B  ← HEAD (main)
           C and D changes are STAGED

git reset --mixed HEAD~2:
           A ── B  ← HEAD (main)
           C and D changes are in WORKING DIRECTORY (unstaged)

git reset --hard HEAD~2:
           A ── B  ← HEAD (main)
           C and D changes are GONE
```

---

## Merge vs Rebase History

```
MERGE (preserves full history):
  A ── B ── C ── D ── M
              \       ↑
               E ── F─┘    M = merge commit connecting both histories

REBASE (linear history):
  A ── B ── C ── D ── E' ── F'
                            Looks like feature was always on top of main
                            E' and F' are replayed commits

Use merge:   for shared/public branches, preserving context
Use rebase:  for local cleanup, keeping history readable
```

---

## Fork Workflow

```
Original repo (upstream)
        │
        ├── Fork on GitHub
        ▼
Your fork (origin)
        │
        ├── git clone
        ▼
Local machine
        │
        ├── create branch
        ├── make commits
        ├── push to YOUR fork (origin)
        ▼
Pull Request: origin → upstream
        │
        └── upstream maintainer reviews + merges

Keeping fork synced:
  git fetch upstream
  git merge upstream/main
  git push origin main
```

---

## Conflict Markers — How to Read Them

```
<<<<<<< HEAD           ← start of YOUR version (current branch)
return "Hello!"
=======                ← separator
return "Hey there!"
>>>>>>> feature-branch ← end of INCOMING version (branch being merged)

Resolution options:
  1. Keep yours:    delete everything except "return "Hello!""
  2. Keep theirs:   delete everything except "return "Hey there!""
  3. Keep both:     return "Hello! Hey there!"
  4. Write new:     return "Greetings!"

After editing — no conflict markers should remain in the file.
Then: git add file.py → git commit
```

---

## Remote Tracking — Ahead / Behind

```
git branch -vv output:

  main      a1b2c3d [origin/main: behind 3]  ← remote has 3 new commits
  feature   b2c3d4e [origin/feature: ahead 2] ← you have 2 commits to push
  develop   c3d4e5f [origin/develop: ahead 1, behind 2] ← diverged

behind N  → run: git pull
ahead N   → run: git push
both      → run: git pull --rebase, then git push
```

---

## HEAD Reference Shortcuts

```
HEAD       = current commit
HEAD~1     = one commit back        (same as HEAD^)
HEAD~3     = three commits back
HEAD^      = first parent of HEAD
HEAD^2     = second parent (only on merge commits)

Example on this history:  A ── B ── C ── D ← HEAD
  HEAD    = D
  HEAD~1  = C
  HEAD~2  = B
  HEAD~3  = A
```
