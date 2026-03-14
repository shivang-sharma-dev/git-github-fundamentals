# 06 — Workflows and Best Practices

> Knowing Git commands is one thing. Knowing HOW teams use Git at scale is what actually gets you hired and makes you effective on day one.

---

## Why Workflows Matter

Without an agreed workflow, teams run into:
- Multiple people committing broken code to main
- "It works on my machine" because branches diverge wildly
- No clear process for releases or hotfixes
- Messy history that's impossible to debug with `git log`

A workflow is a team agreement on: how to branch, when to merge, and how to release.

---

## Workflow 1 — Git Flow

Created by Vincent Driessen in 2010. The classic. Still widely used for software with scheduled releases (mobile apps, versioned libraries, enterprise software).

### The branches in Git Flow

```
main         ← production-ready code only. Every commit is a release.
develop      ← integration branch. All features merge here first.
feature/*    ← one branch per feature, branched from develop
release/*    ← release preparation branch, branched from develop
hotfix/*     ← emergency fixes to production, branched from main
```

### How it flows

```
main ────────────────────────────────────────── v1.0 ──────── v1.1
         \                                       /    \        /
develop   ─────────────────────────────────────/──────────────
              \             /   \            /
feature/login──────────────    release/1.0──
                               \           /
hotfix/                         \         /
payment-bug────────────────────────────────────────── (patch on main + merge to develop)
```

### Feature lifecycle in Git Flow

```bash
# 1. Start a new feature
git switch develop
git pull origin develop
git switch -c feature/user-auth

# 2. Work on feature, commit regularly
git add .
git commit -m "feat: add JWT token generation"

# 3. Finish feature — merge back to develop
git switch develop
git merge --no-ff feature/user-auth
git push origin develop
git branch -d feature/user-auth
```

### Release lifecycle in Git Flow

```bash
# 1. Create release branch from develop (no new features after this)
git switch develop
git switch -c release/1.2.0

# 2. Only bug fixes, version bumps, docs here
git commit -m "chore: bump version to 1.2.0"
git commit -m "fix: edge case in payment validation"

# 3. Finish release — merge to both main AND develop
git switch main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Release 1.2.0"
git push origin main --tags

git switch develop
git merge --no-ff release/1.2.0
git push origin develop

git branch -d release/1.2.0
```

### Hotfix lifecycle in Git Flow

```bash
# Production is broken — fix directly from main
git switch main
git switch -c hotfix/critical-payment-bug

git commit -m "fix: prevent duplicate payment processing"

# Merge back to BOTH main and develop
git switch main
git merge --no-ff hotfix/critical-payment-bug
git tag -a v1.1.1 -m "Hotfix 1.1.1"
git push origin main --tags

git switch develop
git merge --no-ff hotfix/critical-payment-bug
git push origin develop

git branch -d hotfix/critical-payment-bug
```

### When to use Git Flow

- Apps with versioned releases (v1.0, v1.1, v2.0)
- Mobile apps (App Store release cycles)
- Libraries/packages that others depend on
- Teams that need clear separation between in-progress and production code

### When NOT to use Git Flow

- Web apps deployed continuously (multiple times per day)
- Small teams (the overhead becomes a burden)
- Any team practicing CI/CD in the true sense

---

## Workflow 2 — GitHub Flow

Simpler than Git Flow. Created by GitHub. Used for continuous deployment — teams that push to production multiple times a day.

### The rule: there are only two types of branches
1. `main` — always deployable
2. feature branches — everything else

```
main ──────────────────── merge ──────────────── merge
         \               /          \            /
feature/  ───────────────            ────────────
login
```

### GitHub Flow lifecycle

```bash
# 1. Create a branch from main (always from main, not develop)
git switch main
git pull origin main
git switch -c feature/add-dark-mode

# 2. Commit early and often (even works in progress)
git commit -m "wip: dark mode toggle UI"
git commit -m "feat: implement dark mode CSS variables"
git commit -m "feat: persist dark mode preference in localStorage"

# 3. Push your branch and open a PR immediately (even before done)
git push -u origin feature/add-dark-mode
# Open PR on GitHub — mark it as Draft if not ready for review

# 4. Discuss, get feedback, push more commits
git commit -m "fix: correct dark mode contrast ratio"
git push

# 5. CI passes, PR approved — merge to main
# (usually via GitHub UI — Squash and Merge)

# 6. Deploy immediately after merge
# 7. Delete branch

# 8. Repeat
```

### GitHub Flow principles

- `main` is always in a deployable state — every merge triggers a deployment
- Feature branches are short-lived (days, not weeks)
- Open PRs early for discussion, not just when done (use Draft PRs)
- Deploy after every merge to main (automated)

### When to use GitHub Flow

- Web services, SaaS products
- Teams practicing continuous deployment
- Small to medium teams
- Projects that don't need multiple release versions in production simultaneously

---

## Workflow 3 — Trunk-Based Development

The most advanced workflow. Used by Google, Facebook, and high-performing DevOps teams. Developers commit directly to `main` (the trunk) or use very short-lived feature branches (1-2 days max).

### Core idea

```
main ──────────────────────────────────────── (everyone commits here)
(every commit triggers CI, every commit is potentially deployable)
```

Feature branches (if used) live for 24-48 hours maximum. Long-lived branches are considered a failure.

```bash
# Short-lived branch model (most common TBD variant)
git switch -c feature/add-endpoint   # create today
# small, focused commits
git push origin feature/add-endpoint
# PR opened, reviewed, merged SAME DAY
git push origin --delete feature/add-endpoint
```

### Feature flags — the secret ingredient

How do you commit incomplete features to main without breaking production? **Feature flags.**

```python
# Feature flag in code
if feature_flags.is_enabled("new_checkout_flow", user):
    return new_checkout_flow()
else:
    return legacy_checkout_flow()
```

You merge the code but the feature is hidden behind a flag. When the feature is complete and tested, you flip the flag on (without a code change).

### When to use Trunk-Based Development

- Senior teams with high test coverage and automated CI/CD
- Organizations at Google/Netflix/Amazon scale
- When deploying to production many times per day
- Teams that have invested heavily in automated testing

### When NOT to use TBD

- Junior-heavy teams (requires discipline and strong CI)
- No automated testing (would constantly break main)
- Regulated industries requiring strict review processes

---

## Workflow Comparison

| | Git Flow | GitHub Flow | Trunk-Based |
|---|---|---|---|
| Branches | 5 types | 2 types | 1 (or 2 briefly) |
| Release cadence | Scheduled | Continuous | Continuous |
| Complexity | High | Low | Low (discipline) |
| Best for | Versioned releases | SaaS, web apps | Elite CI/CD teams |
| PR required | Yes | Yes | Sometimes |
| Deploy frequency | Weekly/monthly | Daily | Multiple times/day |

---

## Conventional Commits

A specification for writing consistent, machine-readable commit messages. Used by major open source projects (Angular, Vue, etc.) and enables automatic changelog generation and semantic versioning.

### Format

```
<type>(<optional scope>): <short description>

<optional body>

<optional footer>
```

### Types

| Type | Used for |
|---|---|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `style` | Formatting, missing semicolons — no logic change |
| `refactor` | Code restructured — not a feature or fix |
| `test` | Adding or fixing tests |
| `chore` | Build process, dependency updates, tooling |
| `perf` | Performance improvement |
| `ci` | CI/CD configuration changes |
| `build` | Build system changes (webpack, npm scripts) |
| `revert` | Reverts a previous commit |

### Examples

```bash
# Simple feature
git commit -m "feat: add user profile page"

# Feature with scope (what area it affects)
git commit -m "feat(auth): implement OAuth2 login with Google"

# Bug fix referencing an issue
git commit -m "fix(api): correct pagination offset calculation

The offset was calculated as page * limit instead of (page-1) * limit,
causing the first page to be skipped.

Fixes #143"

# Breaking change (major version bump)
git commit -m "feat!: change API response format to JSON:API spec

BREAKING CHANGE: The response format has changed. Clients must update
to parse the new data structure.

Migration guide: https://docs.example.com/migration/v2"

# Documentation
git commit -m "docs: add API authentication guide to README"

# Chore
git commit -m "chore: upgrade dependencies to latest versions"
git commit -m "chore(deps): bump lodash from 4.17.20 to 4.17.21"
```

### Why use Conventional Commits?

1. **Automatic changelogs** — tools like `standard-version` read your commits and generate `CHANGELOG.md` automatically
2. **Semantic versioning** — `feat` = minor bump, `fix` = patch bump, `feat!` = major bump
3. **Readable history** — new team members immediately understand the purpose of any commit
4. **Searchable** — `git log --grep="^feat"` shows only feature commits

---

## Semantic Versioning (SemVer)

Format: `MAJOR.MINOR.PATCH` — e.g., `2.4.1`

| Part | When to increment | Example |
|---|---|---|
| **MAJOR** | Breaking changes (incompatible API changes) | `1.5.3` → `2.0.0` |
| **MINOR** | New features (backward compatible) | `1.5.3` → `1.6.0` |
| **PATCH** | Bug fixes (backward compatible) | `1.5.3` → `1.5.4` |

```bash
# Tag your releases with SemVer
git tag -a v2.4.1 -m "Release 2.4.1 — fix payment edge case"
git push origin v2.4.1
```

Pre-release versions:
```
v1.0.0-alpha.1     ← very early, unstable
v1.0.0-beta.2      ← feature complete, may have bugs
v1.0.0-rc.1        ← release candidate, almost final
v1.0.0             ← stable release
```

---

## Writing Good Commit Messages

The seven rules (from "How to Write a Git Commit Message" by Chris Beams):

1. **Separate subject from body with a blank line**
2. **Limit the subject line to 72 characters**
3. **Use the imperative mood** ("Add feature" not "Added feature" or "Adding feature")
4. **Do not end the subject line with a period**
5. **Use the body to explain WHAT and WHY, not HOW**
6. **Wrap the body at 72 characters**
7. **Reference issues and PRs in the footer**

```bash
# Good:
feat: add rate limiting to authentication endpoints

Without rate limiting, the login endpoint was vulnerable to brute
force attacks. This adds a sliding window limiter of 5 attempts
per 15 minutes per IP address.

Closes #89

# Bad:
git commit -m "fixed stuff"
git commit -m "WIP"
git commit -m "asdfgh"
git commit -m "changes"
git commit -m "John's edits"
```

**Imperative mood test:** Your commit message should complete this sentence:
> "If applied, this commit will ___________"

- "If applied, this commit will **add rate limiting**" ✓
- "If applied, this commit will **fixed stuff**" ✗

---

## Keeping a Clean Repository

```bash
# Remove branches already merged into main
git branch --merged main | grep -v "main" | xargs git branch -d

# Remove stale remote-tracking branches
git fetch --prune

# Remove untracked files and folders (dry run first)
git clean -n
git clean -fd

# Compress and optimize the .git folder
git gc

# Find large files in history (useful before public releases)
git rev-list --objects --all | sort -k 2 | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  grep blob | sort -k3 -n -r | head -20
```

---

## `.gitattributes` — Worth Knowing

Controls how Git treats files — useful for cross-platform teams (Windows/Mac/Linux line ending differences).

```gitattributes
# .gitattributes in root of repo

# Auto-detect text files and normalize line endings
* text=auto

# Force LF for shell scripts (even on Windows)
*.sh text eol=lf

# Treat binary files as binary (don't try to diff them)
*.png binary
*.jpg binary
*.pdf binary

# Custom diff for certain file types
*.json diff=json
```

---

## DevOps-Specific Git Practices

### 1. Sign your commits (GPG signing)
```bash
# Generate GPG key
gpg --full-generate-key

# List your keys
gpg --list-secret-keys --keyid-format LONG

# Configure Git to sign commits
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true

# Signed commit
git commit -S -m "feat: signed commit"
```

GitHub shows "Verified" badge on signed commits — important for regulated environments.

### 2. Git hooks — automate quality checks
Git hooks run scripts at specific points (before commit, before push, etc.).

```bash
# Pre-commit hook: run linter before every commit
# .git/hooks/pre-commit
#!/bin/sh
npm run lint
if [ $? -ne 0 ]; then
  echo "Linting failed. Commit aborted."
  exit 1
fi
```

Use `husky` (Node.js) or `pre-commit` (Python) to manage hooks across team members.

### 3. Monorepo vs polyrepo

| | Monorepo (all code in one repo) | Polyrepo (one repo per service) |
|---|---|---|
| Used by | Google, Meta, Twitter | Most startups, microservice teams |
| Benefits | Easy cross-service changes, unified CI | Independent deployments, clear ownership |
| Tools needed | Nx, Turborepo, Bazel | Simple Git |

### 4. Git in CI/CD pipelines
```yaml
# GitHub Actions — checkout code
- uses: actions/checkout@v3
  with:
    fetch-depth: 0   # fetch full history (needed for changelog generation)

# Get the current tag in a pipeline
- run: echo "VERSION=$(git describe --tags --abbrev=0)" >> $GITHUB_ENV

# Check if this commit changes a specific folder (for monorepos)
- run: |
    if git diff --name-only HEAD^ HEAD | grep -q "^services/api/"; then
      echo "API changed — trigger API deployment"
    fi
```

---

## The DevOps Git Mindset

- **Commit often, push often.** Small commits are easier to review, debug, and revert.
- **Everything in Git.** Not just code — infrastructure configs, docs, CI/CD pipelines, runbooks.
- **History is documentation.** A well-maintained Git log is a team's changelog, decision record, and debugging tool.
- **Automate the boring parts.** Linting, testing, and deployments should run automatically on every push.
- **Protect main.** No one should be able to commit directly to main without a review. Enable branch protection from day one.
- **Treat tags as releases.** Every production deployment should be a tagged commit. This makes rollback trivial.
