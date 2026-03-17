# Practice Projects for Git & GitHub

Hands-on projects to build real Git skills — organized from beginner to advanced. Each project focuses on specific Git/GitHub concepts.

---

## Beginner Projects

### 1. Personal Portfolio Website (GitHub Pages)

**Concepts practiced:** `init`, `add`, `commit`, `push`, GitHub Pages, basic workflow

**What to do:**
- Create a simple HTML/CSS personal portfolio
- Host it on GitHub Pages
- Practice making changes, committing, and pushing updates
- Set up a custom domain (optional)

**Bonus:** Use the `gh-pages` branch workflow

---

### 2. Daily Learning Journal

**Concepts practiced:** Daily commits, commit messages, `.gitignore`, Markdown

**What to do:**
- Create a repo where you write a short Markdown entry every day
- Document what you learned, mistakes made, and key takeaways
- Use meaningful commit messages (practice conventional commits)
- Add a `.gitignore` for any local files you don't want tracked

**Bonus:** This also builds your GitHub contribution graph

---

### 3. Collaborative README Project

**Concepts practiced:** Forking, pull requests, merge conflicts, collaboration

**What to do:**
- Find an open-source "awesome list" or a "first contributions" repo
- Fork it, make a meaningful addition, and submit a Pull Request
- Recommended starting repos:
  - [first-contributions](https://github.com/firstcontributions/first-contributions)
  - [EddieHub/hacktoberfest-practice](https://github.com/EddieHubCommunity/hacktoberfest-practice)
  - [awesome](https://github.com/sindresorhus/awesome) (contribute to any awesome list)

---

### 4. Dotfiles Repository

**Concepts practiced:** Tracking config files, symlinks, `.gitignore`, repo organization

**What to do:**
- Create a repo to store your personal configuration files (`.bashrc`, `.vimrc`, VS Code settings, etc.)
- Write a setup script to symlink them on a new machine
- Practice updating and committing config changes over time

---

## Intermediate Projects

### 5. Team Simulation Project

**Concepts practiced:** Branching, merging, pull requests, code review, merge conflicts

**What to do:**
- Create a simple project (e.g., a static website or a CLI tool)
- Simulate a team workflow by yourself using multiple branches:
  - `main` — stable releases
  - `develop` — integration branch
  - `feature/navbar`, `feature/footer`, etc. — feature branches
- Create PRs from feature branches → develop → main
- Intentionally create and resolve merge conflicts

---

### 6. Open Source Contribution

**Concepts practiced:** Fork workflow, upstream tracking, rebasing, PR etiquette

**What to do:**
- Find a real open source project with `good-first-issue` labels
- Fork it, clone, create a feature branch, make changes, submit a PR
- Where to find issues:
  - [goodfirstissue.dev](https://goodfirstissue.dev)
  - [up-for-grabs.net](https://up-for-grabs.net)
  - [GitHub "good first issue" search](https://github.com/issues?q=label%3A%22good+first+issue%22+is%3Aopen)
  - [First Timers Only](https://www.firsttimersonly.com)

---

### 7. Release Management Project

**Concepts practiced:** Tags, releases, semantic versioning, changelogs

**What to do:**
- Build a small CLI tool or utility (in any language)
- Use semantic versioning: `v1.0.0`, `v1.1.0`, `v2.0.0`
- Create Git tags for each version
- Write a `CHANGELOG.md` documenting changes per version
- Create GitHub Releases with release notes

---

### 8. Git Hooks Playground

**Concepts practiced:** Git hooks, automation, pre-commit checks

**What to do:**
- Create a project with custom Git hooks:
  - `pre-commit`: Lint code before allowing a commit
  - `commit-msg`: Enforce conventional commit message format
  - `pre-push`: Run tests before pushing
- Try using [Husky](https://typicode.github.io/husky/) (for Node.js projects) or [pre-commit](https://pre-commit.com) (Python-based, language-agnostic)

---

## Advanced Projects

### 9. CI/CD Pipeline with GitHub Actions

**Concepts practiced:** GitHub Actions, YAML workflows, automated testing, deployment

**What to do:**
- Take any project and add a `.github/workflows/` pipeline:
  - Run linting on every push
  - Run tests on every PR
  - Auto-deploy to GitHub Pages / Vercel / Netlify on merge to `main`
- Practice workflow triggers: `push`, `pull_request`, `schedule`, `workflow_dispatch`

**Starter workflow ideas:**
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm test
```

---

### 10. Monorepo Management

**Concepts practiced:** Monorepo structure, git submodules or subtrees, selective builds

**What to do:**
- Create a monorepo with multiple packages/services:
  ```
  monorepo/
  ├── packages/
  │   ├── frontend/
  │   ├── backend/
  │   └── shared/
  └── .github/workflows/
  ```
- Set up GitHub Actions that only build the changed package
- Practice using path filters in CI

---

### 11. Git Bisect Bug Hunt

**Concepts practiced:** `git bisect`, debugging with Git history

**What to do:**
- Create a project with ~20+ commits
- Intentionally introduce a bug at some commit in the middle
- Use `git bisect` to find exactly which commit introduced the bug
- Document the process

---

### 12. Repository Migration & Cleanup

**Concepts practiced:** `filter-branch`, `filter-repo`, BFG Repo Cleaner, history rewriting

**What to do:**
- Create a test repo with:
  - Accidentally committed large files
  - Sensitive data in commit history
- Use `git filter-repo` or BFG Repo Cleaner to:
  - Remove large files from history
  - Remove sensitive data
  - Rewrite author information
- **Warning:** Practice this on test repos only, never on production repos without backups

---

## Project Ideas by Tech Stack

| Tech Stack | Project Idea | Git Concepts |
|-----------|--------------|--------------|
| HTML/CSS | Personal portfolio on GitHub Pages | Pages, branching, deployment |
| JavaScript | Todo app with CI/CD pipeline | Actions, automated testing |
| Python | CLI tool with versioned releases | Tags, releases, semantic versioning |
| React/Vue | Component library with PR workflow | Feature branches, code review |
| Node.js | REST API with branch protection | Protected branches, required reviews |
| Any | Contributing to open source | Fork, upstream, rebase, PR workflow |
| Any | Automate your workflow with hooks | Git hooks, pre-commit, Husky |

---

## Open Source Programs to Participate In

These programs encourage open source contributions and are great for practicing Git:

| Program | When | Description |
|---------|------|-------------|
| [Hacktoberfest](https://hacktoberfest.com) | October (yearly) | Make 4 PRs to open source repos, earn swag |
| [Google Summer of Code (GSoC)](https://summerofcode.withgoogle.com) | March - September | Work on open source projects with mentors |
| [MLH Fellowship](https://fellowship.mlh.io) | Multiple batches/year | Open source contributions with stipend |
| [GirlScript Summer of Code (GSSoC)](https://gssoc.girlscript.tech) | March - May | Beginner-friendly open source program |
| [Outreachy](https://www.outreachy.org) | Twice a year | Paid internships in open source for underrepresented groups |
| [Season of KDE](https://season.kde.org) | Yearly | Contribute to KDE projects |
| [CNCF Mentorships](https://www.cncf.io/people/community/) | Multiple times/year | Cloud-native open source mentorships |

---

> **Tip:** Start with Project #2 (Daily Journal) to build consistency, then move to Project #6 (Open Source Contribution) to get real-world collaboration experience. These two alone will teach you 80% of what you need.
