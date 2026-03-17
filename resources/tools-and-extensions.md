# Git Tools & Extensions

A curated list of tools, VS Code extensions, GUI clients, and utilities that make working with Git more productive.

---

## VS Code Extensions

### Essential

| Extension | Description |
|-----------|-------------|
| **GitLens** | The most powerful Git extension. Inline blame, file history, commit search, visual compare, and much more. |
| **GitHub Pull Requests and Issues** | Review and manage PRs and issues directly from VS Code. |
| **Git Graph** | Visualize your repo's branch history as an interactive graph. |
| **GitHub Copilot** | AI-powered code suggestions (free for students and OSS contributors). |

### Useful Additions

| Extension | Description |
|-----------|-------------|
| **Git History** | View file history, compare branches, and search commits visually. |
| **GitHub Actions** | View and manage GitHub Actions workflows from VS Code. |
| **Conventional Commits** | Helps write commit messages following the conventional commits standard. |
| **GitHub Repositories** | Browse and edit any GitHub repo without cloning it locally. |
| **Remote Repositories** | Open any GitHub repo directly in VS Code without cloning. |
| **GitHub Codespaces** | Develop in cloud-hosted dev environments from VS Code. |
| **Markdown All in One** | Useful for editing README.md and documentation files. |
| **markdownlint** | Lint your Markdown files to keep docs clean and consistent. |

---

## Git GUI Clients

For those who prefer a visual interface alongside the command line.

### Free

| Tool | Platform | Description |
|------|----------|-------------|
| **GitHub Desktop** | Windows, macOS | Official GitHub client. Simple, clean, beginner-friendly. |
| **Sourcetree** | Windows, macOS | Feature-rich GUI by Atlassian. Great for visualizing branches. |
| **GitKraken (Free Tier)** | Windows, macOS, Linux | Beautiful UI, visual commit graph. Free for public repos. |
| **VS Code (Built-in)** | All platforms | Built-in source control panel handles staging, commits, diffs, and branches. |
| **Lazygit** | All platforms (terminal) | Terminal-based Git UI. Fast, keyboard-driven, runs in the terminal. |
| **Gitg** | Linux | GNOME-native Git client. Simple and lightweight. |
| **Git Cola** | All platforms | Lightweight Qt-based Git GUI. |

### Paid

| Tool | Platform | Description |
|------|----------|-------------|
| **GitKraken (Pro)** | All platforms | Full features including private repo support, merge conflict editor. |
| **Tower** | Windows, macOS | Professional Git client with advanced features like interactive rebase UI. |
| **Sublime Merge** | All platforms | From the makers of Sublime Text. Fast, clean, powerful diff viewer. |
| **Fork** | Windows, macOS | Fast and friendly Git client with great merge conflict resolver. |

---

## Command Line Tools

### Git Enhancements

| Tool | Description |
|------|-------------|
| **gh (GitHub CLI)** | Official GitHub CLI. Create PRs, issues, manage repos, trigger Actions — all from the terminal. |
| **tig** | Text-mode interface for Git. Browse commits, diffs, and logs in a ncurses UI. |
| **delta** | A syntax-highlighting pager for `git diff`, `git log`, and `git show` output. |
| **diff-so-fancy** | Makes `git diff` output human-readable and beautiful. |
| **git-extras** | Collection of 60+ useful Git utilities (e.g., `git summary`, `git effort`, `git changelog`). |
| **hub** | GitHub extension for the `git` command (predecessor to `gh` CLI). |
| **fzf + git** | Fuzzy finder integration for Git — search branches, commits, and files interactively. |
| **bat** | A `cat` replacement with syntax highlighting — useful for viewing file content in Git workflows. |

### Git Workflow Tools

| Tool | Description |
|------|-------------|
| **pre-commit** | Framework for managing Git hooks. Language-agnostic, huge plugin ecosystem. |
| **Husky** | Git hooks made easy for Node.js/JavaScript projects. |
| **commitlint** | Lint commit messages against conventional commit standards. |
| **commitizen** | Interactive CLI to write conventional commits step by step. |
| **semantic-release** | Fully automated version management and publishing based on commit messages. |
| **standard-version** | Automates versioning and CHANGELOG generation using conventional commits. |
| **git-flow (AVH)** | Git extension for the Git Flow branching model. |

---

## Git History & Cleanup Tools

| Tool | Description |
|------|-------------|
| **git filter-repo** | Recommended tool for rewriting Git history (replaces `filter-branch`). |
| **BFG Repo-Cleaner** | Faster alternative to `git filter-branch` for removing large files or secrets from history. |
| **git-sizer** | Analyze repo for size issues (large files, deep history, etc.). |
| **git-lfs** | Git Large File Storage — track large files (images, datasets, binaries) without bloating the repo. |

---

## Online Tools

| Tool | Description |
|------|-------------|
| [gitignore.io](https://www.toptal.com/developers/gitignore) | Generate `.gitignore` files for any language, framework, or IDE. |
| [GitHub Profile README Generator](https://rahuldkjain.github.io/gh-profile-readme-generator/) | Build a fancy GitHub profile README with stats and badges. |
| [Shields.io](https://shields.io) | Generate badges for your README (build status, license, version, etc.). |
| [GitHub Readme Stats](https://github.com/anuraghazra/github-readme-stats) | Dynamically generated GitHub stats for your profile README. |
| [Conventional Changelog](https://github.com/conventional-changelog/conventional-changelog) | Generate changelogs from conventional commit messages. |
| [Git Explorer](https://gitexplorer.com) | Find the right Git command for what you're trying to do. |

---

## Recommended Setup for Beginners

If you're just starting out, install these:

```
1. Git                    → The core tool (git-scm.com)
2. GitHub CLI (gh)        → Interact with GitHub from terminal
3. VS Code                → Code editor with built-in Git support
4. GitLens (VS Code ext)  → Supercharges Git inside VS Code
5. Git Graph (VS Code ext)→ Visualize branches and history
6. delta                  → Better diffs in the terminal
```

## Recommended Setup for Advanced Users

```
1. All of the above, plus:
2. lazygit                → Terminal UI for Git (fast workflow)
3. pre-commit             → Automate code quality checks
4. commitizen             → Write conventional commits easily
5. fzf                    → Fuzzy find branches, commits, files
6. git filter-repo        → For when you need to rewrite history
7. git-lfs               → If working with large files
```

---

> **Tip:** You don't need all of these. Start with Git + VS Code + GitLens + GitHub CLI. Add more tools as your workflow demands them.
