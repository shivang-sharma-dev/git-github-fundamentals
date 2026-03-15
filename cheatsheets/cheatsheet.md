# Git Cheatsheet

> Quick reference for all essential Git commands. No explanations — just commands.

---

## Setup and Config

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global core.editor "vim"
git config --global init.defaultBranch main
git config --global color.ui auto
git config --global pull.rebase true

git config --list                        # list all config
git config user.name                     # check a value
cat ~/.gitconfig                         # view config file
```

---

## SSH Key Setup

```bash
ssh-keygen -t ed25519 -C "you@example.com"  # generate key
cat ~/.ssh/id_ed25519.pub                    # copy this to GitHub
ssh -T git@github.com                        # test connection
```

---

## Starting a Repo

```bash
git init                                 # init in current folder
git init my-project                      # init in new folder
git clone git@github.com:user/repo.git   # clone via SSH
git clone https://github.com/user/repo  # clone via HTTPS
git clone <url> my-folder               # clone into custom name
git clone --depth 1 <url>               # shallow clone
git clone -b develop <url>              # clone specific branch
```

---

## Daily Workflow

```bash
git status                               # what's going on
git status -s                            # short output

git add file.txt                         # stage a file
git add .                                # stage all changes
git add -A                               # stage everything including deletions
git add -p file.txt                      # stage parts of a file interactively

git commit -m "feat: add login page"     # commit with message
git commit                               # open editor for message
git commit -am "fix: typo"              # stage tracked + commit
git commit --amend -m "new message"      # fix last commit message
git commit --amend --no-edit             # add to last commit, keep message

git log                                  # full log
git log --oneline                        # one line per commit
git log --oneline --graph --all          # visual branch graph
git log -5                               # last 5 commits
git log --author="Alice"                 # filter by author
git log --grep="login"                   # search commit messages
git log -- README.md                     # commits touching a file
git log --after="2024-01-01"             # commits after date

git diff                                 # unstaged changes
git diff --staged                        # staged changes
git diff main feature                    # diff between branches
git diff a1b2c3d e4f5a6b                 # diff between commits
git diff --stat                          # summary only

git show                                 # show latest commit
git show a1b2c3d                         # show specific commit
git show a1b2c3d:path/to/file.py         # show file at commit
```

---

## Undoing Things

```bash
git restore file.txt                     # discard unstaged changes
git restore .                            # discard ALL unstaged changes
git restore --staged file.txt            # unstage a file
git restore --source a1b2c3d file.txt    # restore file from commit

git reset --soft HEAD~1                  # undo commit, keep staged
git reset --mixed HEAD~1                 # undo commit, keep in working dir
git reset --hard HEAD~1                  # undo commit, delete changes
git reset --hard origin/main             # reset to match remote

git revert a1b2c3d                       # new commit that undoes a commit
git revert --no-commit a1b2c3d           # revert without committing

git commit --amend -m "corrected"        # fix last commit (before push only)
```

---

## Branching

```bash
git branch                               # list local branches
git branch -r                            # list remote branches
git branch -a                            # list all branches
git branch -vv                           # show tracking info

git branch feature-login                 # create branch (stay here)
git switch feature-login                 # switch to branch
git switch -c feature-login              # create AND switch
git switch -c hotfix v1.2.0             # branch from a tag
git switch -c debug a1b2c3d             # branch from a commit

git branch -m new-name                   # rename current branch
git branch -m old new                    # rename specific branch

git branch -d feature-login              # delete (safe)
git branch -D feature-login              # force delete

git branch --merged                      # branches merged into current
git branch --no-merged                   # branches NOT yet merged

# Clean up merged branches
git branch --merged main | grep -v "main" | xargs git branch -d
```

---

## Merging

```bash
git merge feature-login                  # merge into current branch
git merge --no-ff feature-login          # always create merge commit
git merge --squash feature-login         # squash into one commit
git merge --abort                        # abort a merge in progress

# After resolving conflicts:
git add resolved-file.py
git commit
```

---

## Rebasing

```bash
git rebase main                          # rebase current branch onto main
git rebase --continue                    # continue after conflict
git rebase --abort                       # abort rebase
git rebase --skip                        # skip current conflicting commit

git rebase -i HEAD~3                     # interactive rebase last 3 commits
git rebase -i a1b2c3d                    # interactive from a commit

# Interactive rebase actions:
# pick   p  — keep commit
# reword r  — edit message
# squash s  — combine with previous, merge messages
# fixup  f  — combine with previous, discard message
# drop   d  — delete commit
# edit   e  — pause to amend
```

---

## Cherry-Pick

```bash
git cherry-pick a1b2c3d                  # apply a commit to current branch
git cherry-pick a1b2c3d e4f5a6b          # apply multiple commits
git cherry-pick a1b2c3d..e4f5a6b         # apply a range
git cherry-pick --no-commit a1b2c3d      # apply changes without committing
git cherry-pick --abort                  # abort
```

---

## Remote Operations

```bash
git remote -v                            # list remotes
git remote add origin <url>              # add remote
git remote add upstream <url>            # add upstream (forks)
git remote remove upstream               # remove remote
git remote rename origin old-origin      # rename remote
git remote set-url origin <url>          # change remote URL

git fetch origin                         # download, don't merge
git fetch --all                          # fetch from all remotes
git fetch --prune                        # fetch + clean stale branches

git pull                                 # fetch + merge
git pull --rebase                        # fetch + rebase
git pull origin main                     # pull specific branch

git push                                 # push current branch
git push -u origin feature-login         # push + set upstream
git push origin --all                    # push all branches
git push origin --tags                   # push all tags
git push origin v1.0.0                   # push specific tag
git push --force-with-lease              # safe force push
git push origin --delete old-branch      # delete remote branch

# Sync fork with upstream
git fetch upstream
git merge upstream/main
git push origin main
```

---

## Stash

```bash
git stash                                # save current changes
git stash save "wip: login feature"      # save with message
git stash list                           # list all stashes
git stash pop                            # apply latest + remove from list
git stash apply                          # apply latest, keep in list
git stash apply stash@{2}               # apply specific stash
git stash drop stash@{0}                # delete specific stash
git stash clear                          # delete all stashes
git stash show -p stash@{0}             # see stash contents
git stash branch new-branch stash@{0}   # create branch from stash
```

---

## Tags

```bash
git tag                                  # list all tags
git tag -l "v1.*"                        # filter tags
git tag v1.0.0                           # lightweight tag
git tag -a v1.0.0 -m "Release 1.0.0"    # annotated tag (use this)
git tag -a v0.9.0 a1b2c3d -m "Beta"     # tag a past commit
git show v1.0.0                          # see tag details
git push origin v1.0.0                   # push one tag
git push origin --tags                   # push all tags
git tag -d v1.0.0                        # delete local tag
git push origin --delete v1.0.0          # delete remote tag
```

---

## Inspecting and Searching

```bash
git blame README.md                      # who changed each line
git blame -L 10,20 README.md            # blame specific lines

git grep "TODO"                          # search in all tracked files
git grep -n "function login"             # with line numbers
git grep -l "import os"                  # only filenames

git bisect start                         # start binary bug search
git bisect bad                           # current version is broken
git bisect good v1.0.0                   # this version was fine
git bisect good / git bisect bad         # mark each checkpoint
git bisect reset                         # finish bisect

git shortlog -sn                         # commits per author
git log --stat                           # files changed per commit
```

---

## File Operations

```bash
git rm file.txt                          # delete file + stage deletion
git rm --cached file.txt                 # untrack file, keep on disk
git mv old.py new.py                     # rename/move file
git clean -n                             # dry run — show what would be deleted
git clean -fd                            # delete untracked files + folders
git clean -fdx                           # also delete ignored files
```

---

## .gitignore Patterns

```gitignore
secret.env                   # ignore specific file
*.log                        # ignore by extension
*.pyc
build/                       # ignore folder
node_modules/
__pycache__/
**/logs                      # ignore in any subdirectory
logs/*                       # ignore folder contents
!logs/.gitkeep               # except this file
/TODO                        # only in root, not subdirectories
doc/**/*.txt                 # all .txt inside doc/
```

```bash
# Untrack a file that was already committed
git rm --cached secret.env
echo "secret.env" >> .gitignore
git commit -m "chore: stop tracking secret.env"

# Global gitignore
git config --global core.excludesfile ~/.gitignore_global
```

---

## Useful One-Liners

```bash
# Undo last commit but keep changes
git reset --soft HEAD~1

# Completely discard all local changes
git reset --hard HEAD

# See what changed in last commit
git show --stat HEAD

# Find which branch contains a commit
git branch --contains a1b2c3d

# List commits not yet pushed
git log origin/main..HEAD

# See difference between local and remote
git diff HEAD origin/main

# Delete all local branches already merged to main
git branch --merged main | grep -v "main" | xargs git branch -d

# Remove stale remote-tracking branches
git fetch --prune

# Compress git repo
git gc

# Count commits by author
git shortlog -sn

# Show file at a specific commit without checking out
git show a1b2c3d:src/app.py

# Rename last commit
git commit --amend -m "corrected message"

# Unstage everything
git restore --staged .

# See all tags sorted by version
git tag -l | sort -V
```

---

## Conventional Commit Types

```
feat      new feature
fix       bug fix
docs      documentation only
style     formatting, no logic change
refactor  restructured code, no feature/fix
test      adding or fixing tests
chore     dependencies, build process, tooling
perf      performance improvement
ci        CI/CD config changes
build     build system changes
revert    reverts a previous commit
```

```bash
# Format
git commit -m "type(scope): short description"
git commit -m "feat(auth): add Google OAuth login"
git commit -m "fix(api): correct pagination offset"
git commit -m "docs: update README with setup steps"

# Breaking change
git commit -m "feat!: change API response to JSON:API format"
```

---

## Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR  breaking changes          1.0.0 → 2.0.0
MINOR  new features              1.0.0 → 1.1.0
PATCH  bug fixes                 1.0.0 → 1.0.1

Pre-release:
v1.0.0-alpha.1
v1.0.0-beta.2
v1.0.0-rc.1
v1.0.0
```

---

## Reset vs Revert vs Restore

| Command | Affects | Safe on shared branch? |
|---|---|---|
| `git restore` | Working dir / staging area only | Yes |
| `git reset` | Moves HEAD + branch pointer | No — rewrites history |
| `git revert` | Creates new undo commit | Yes — safe always |

---

## HEAD References

```bash
HEAD          current commit
HEAD~1        one commit before HEAD
HEAD~3        three commits before HEAD
HEAD^         first parent of HEAD (same as HEAD~1)
HEAD^2        second parent (merge commits)
a1b2c3d       specific commit hash
main@{1}      where main was 1 move ago
@{yesterday}  HEAD as of yesterday
```
