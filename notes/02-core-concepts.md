# 02 — Core Concepts

> Most people learn Git commands by trial and error and never understand what's actually happening underneath. This file fixes that. Understanding these concepts will make every Git command make sense.

---

## The Three Areas (Most Important Concept in Git)

Every file in a Git project lives in one of three areas at any given time. Understanding these areas is the key to understanding Git.

```
┌─────────────────┐     git add      ┌─────────────────┐    git commit    ┌─────────────────┐
│                 │ ──────────────►  │                 │ ──────────────► │                 │
│ Working         │                  │ Staging Area    │                 │ Repository      │
│ Directory       │ ◄──────────────  │ (Index)         │                 │ (.git folder)   │
│                 │   git restore    │                 │                 │                 │
└─────────────────┘                  └─────────────────┘                 └─────────────────┘
  Your actual files                   Files ready to                      Permanent history
  on disk                             be committed                        of commits
```

### 1. Working Directory

This is the folder you see on your computer — your actual files. When you open a file in VS Code and edit it, you're working in the working directory.

Changes here are **not tracked yet**. Git sees them as "modified" or "untracked" but hasn't done anything with them.

### 2. Staging Area (also called the Index)

A preparation zone. You choose exactly which changes go into the next commit by `git add`-ing them to the staging area.

This gives you fine-grained control — you might have changed 5 files but only want to commit 2 of them. Stage only those 2.

### 3. Repository (the `.git` folder)

The hidden `.git` folder inside your project. This is where Git stores the entire history of your project — every commit, every branch, every tag.

When you run `git commit`, the staged changes get permanently saved here as a new commit object.

---

## The File Lifecycle

A file can be in one of these states:

```
New file created
      │
      ▼
 Untracked ──── git add ────► Staged ──── git commit ────► Committed (Unmodified)
      ▲                                                            │
      │                                                     (you edit the file)
      │                                                            │
      └──────── git rm ────────────────────────────────── Modified (tracked)
                                                                   │ s
                                                            git add again ──► Staged
```

| State                      | Meaning                                                              |
| -------------------------- | -------------------------------------------------------------------- |
| **Untracked**              | Git has never seen this file before. New files start here.           |
| **Staged**                 | File is in the staging area, ready to be included in the next commit |
| **Committed / Unmodified** | File is saved in the repository, no changes since last commit        |
| **Modified**               | File has been changed since the last commit but not staged yet       |

```bash
# Check what state your files are in
git status
```

---

## What is a Commit?

This is critical: **a commit is a snapshot, not a diff.**

Many people think Git stores the changes between versions (like a list of edits). It doesn't. Git stores a complete snapshot of all your files at the moment you commit.

What a commit actually contains:

- A **unique SHA-1 hash** (like `a1b2c3d`) — a fingerprint of this exact commit
- A **pointer to the tree** (your file snapshot)
- A **pointer to the parent commit** (or commits, in a merge)
- **Author name and email**
- **Timestamp**
- **Your commit message**

```bash
# See exactly what's inside a commit
git show a1b2c3d

# See the full commit hash
git log --format="%H %s"
```

Why snapshots and not diffs? Because retrieving any version is fast — Git just reads that snapshot directly instead of replaying all the diffs from the beginning.

> Git does compress and deduplicate internally (files that haven't changed share the same object), so it doesn't waste space.

---

## What is HEAD?

HEAD is a pointer that tells Git: **"where am I right now?"**

Normally, HEAD points to the tip (latest commit) of the branch you're currently on.

```
main branch commits:
  A ── B ── C ── D  ← main
                 ↑
               HEAD
```

When you make a new commit, HEAD (and the branch pointer) move forward:

```
  A ── B ── C ── D ── E  ← main
                      ↑
                    HEAD
```

When you switch branches, HEAD moves to point to that branch:

```bash
git checkout feature-branch
```

```
main:    A ── B ── C ── D
                         \
feature-branch:           E ── F  ← feature-branch
                               ↑
                             HEAD
```

### Detached HEAD state

If you checkout a specific commit (not a branch), HEAD points directly to that commit instead of a branch. This is called "detached HEAD":

```bash
git checkout a1b2c3d   # Now HEAD points to a commit, not a branch
```

Any commits you make in detached HEAD state will be lost when you switch branches (unless you create a branch from there). It's not dangerous — just be aware of it.

```bash
# Fix detached HEAD by creating a branch from it
git checkout -b my-recovery-branch
```

---

## The `.git` Folder — What's Inside

When you run `git init`, Git creates a `.git` folder. This IS your repository — if you delete it, you lose all history.

```
.git/
├── HEAD              ← points to current branch (contains "ref: refs/heads/main")
├── config            ← repo-specific config (local git config)
├── description       ← used by GitWeb, not important for us
├── index             ← the staging area (binary file)
├── objects/          ← all your file contents, commits, trees stored here
│   ├── pack/         ← compressed object storage
│   └── (loose objects named by hash)
└── refs/
    ├── heads/        ← local branch pointers (one file per branch)
    │   └── main      ← contains the hash of the latest commit on main
    └── tags/         ← tag pointers
```

### The Objects directory — Git's storage engine

Git stores everything as "objects" in `.git/objects/`. There are 4 types:

| Object type | Contains                                            | Created by   |
| ----------- | --------------------------------------------------- | ------------ |
| **blob**    | Contents of a single file                           | `git add`    |
| **tree**    | A directory listing (list of blobs and other trees) | `git commit` |
| **commit**  | Snapshot metadata (tree, parent, author, message)   | `git commit` |
| **tag**     | Points to a commit with extra metadata              | `git tag -a` |

```bash
# See all objects in your repo
git cat-file --batch-all-objects --batch-check

# See the type and content of any object
git cat-file -t a1b2c3d   # type
git cat-file -p a1b2c3d   # pretty print content
```

---

## How Branches Actually Work

A branch is just a **lightweight pointer** (a text file containing a commit hash). That's it. There's no copying of files, no duplication of history.

```bash
cat .git/refs/heads/main
# a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0
```

Creating a branch = creating a new text file with a commit hash. This is why branching in Git is near-instant.

```
Before branching:
  A ── B ── C  ← main, HEAD

After git checkout -b feature:
  A ── B ── C  ← main
             ↑
          feature, HEAD

After a commit on feature:
  A ── B ── C  ← main
              \
               D  ← feature, HEAD
```

---

## How Merging Works (preview)

When you merge two branches, Git finds the **common ancestor** commit, then combines the changes from both branches since that point.

```
         common ancestor
              │
  A ── B ── C ── D  ← main
              \
               E ── F  ← feature
```

After `git merge feature` while on main:

```
  A ── B ── C ── D ── M  ← main (M is the merge commit)
              \       /
               E ── F  ← feature
```

---

## Understanding the SHA-1 Hash

Every object in Git has a unique 40-character hex identifier called a SHA-1 hash. Example:

```
a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0
```

This hash is computed from the content of the object. If even one byte changes, the hash completely changes. This is how Git ensures data integrity — you can always verify nothing was corrupted.

In practice, you rarely type the full 40 characters. Git accepts the shortest prefix that's unique:

```bash
git show a1b2c3d   # as few as 7 characters usually works
```

---

## The Index (Staging Area) in Detail

The staging area (also called the "index") lives at `.git/index`. It's a binary file that acts as a "next commit draft."

Think of it as a table with columns: filename, permissions, file hash, and stage number.

```bash
# See what's in the index right now
git ls-files --stage
```

When you `git add` a file:

1. Git computes the SHA-1 of the file content
2. Stores the file content as a blob object in `.git/objects/`
3. Updates the index to point to that blob

When you `git commit`:

1. Git reads the index to know what files to include
2. Creates tree objects from the index entries
3. Creates a commit object pointing to the root tree, the parent commit, and your message
4. Updates the branch pointer (and HEAD) to the new commit hash

---

## Key Mental Models to Memorize

1. **Three areas**: working directory → staging area → repository. Every Git operation moves data between these.

2. **Commits are snapshots, not diffs.** Each commit is a complete picture of all your files at that moment.

3. **Branches are just pointers.** Creating a branch costs nothing. A branch is a file containing a 40-character hash.

4. **HEAD = "you are here."** HEAD tells Git where you currently are in the history.

5. **The `.git` folder IS your repository.** Everything Git knows is in there.

---

## Common Confusion Cleared Up

**Q: Why do I need to `git add` before `git commit`? Why not just commit directly?**

A: The staging area lets you be selective. You might have 10 modified files but only want to commit 3 related ones. Stage only those 3, commit, then stage and commit the rest separately. This creates cleaner, more meaningful history.

**Q: If I delete the `.git` folder, what happens?**

A: Your current files survive (they're in the working directory), but you lose ALL history. The folder becomes a regular folder — no longer a Git repo.

**Q: Are branches stored separately from commits?**

A: No. All commits are stored in `.git/objects/`. Branches are just pointers into that shared pool of commits. Multiple branches can point to the same commit.
