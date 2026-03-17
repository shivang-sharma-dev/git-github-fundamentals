# Recommended Books for Git & GitHub

A mix of free and paid books covering version control, Git internals, collaboration, and DevOps practices.

---

## Free Books

### 1. Pro Git (Scott Chacon & Ben Straub)

- **Cost:** Completely free
- **Link:** [git-scm.com/book](https://git-scm.com/book/en/v2)
- **Level:** Beginner → Advanced
- **Why read it:** This is THE definitive Git book. Covers everything from basics to Git internals (object model, packfiles, transfer protocols). Written by a GitHub co-founder. Available online and as a free PDF/epub/mobi.

**Best chapters to start with:**
- Chapter 1–3: Basics, branching, merging
- Chapter 5: Distributed workflows
- Chapter 7: Advanced tools (stash, bisect, rewrite history)
- Chapter 10: Git internals

---

### 2. Git from the Bottom Up (John Wiegley)

- **Cost:** Free
- **Link:** [jwiegley.github.io/git-from-the-bottom-up](https://jwiegley.github.io/git-from-the-bottom-up/)
- **Level:** Intermediate → Advanced
- **Why read it:** Teaches Git by explaining how it works internally first — blobs, trees, commits, refs. Once you understand the data model, every command makes intuitive sense. Short and dense.

---

### 3. Think Like (a) Git (Sam Livingston-Gray)

- **Cost:** Free (online)
- **Link:** [think-like-a-git.net](https://think-like-a-git.net)
- **Level:** Intermediate
- **Why read it:** Focuses on building a mental model of Git using graph theory. Helps you stop being afraid of rebase and understand what Git actually does under the hood.

---

### 4. Git Notes for Professionals

- **Cost:** Free (PDF)
- **Link:** [goalkicker.com/GitBook](https://goalkicker.com/GitBook/)
- **Level:** Beginner → Intermediate
- **Why read it:** Compiled from Stack Overflow documentation. A practical, no-nonsense reference book with code examples for every common operation.

---

### 5. GitHub Open Source Guides

- **Cost:** Free
- **Link:** [opensource.guide](https://opensource.guide)
- **Level:** Beginner → Intermediate
- **Why read it:** Not a Git book per se, but essential reading for anyone who wants to contribute to or maintain open source projects on GitHub. Covers finding projects, making contributions, building community, and maintaining projects.

---

## Paid Books

### 6. Git Pocket Guide (Richard E. Silverman)

- **Publisher:** O'Reilly
- **Level:** Beginner → Intermediate
- **Why read it:** A concise, portable reference. Great for quick lookups when you need to remember the exact syntax or behavior of a command. Designed to be a companion, not a textbook.

---

### 7. Version Control with Git, 3rd Edition (Prem Kumar Ponuthorai & Jon Loeliger)

- **Publisher:** O'Reilly
- **Level:** Intermediate → Advanced
- **Why read it:** More in-depth than Pro Git on some topics. Covers merging strategies, patching, hooks, and repo management in detail. Good for developers who want a deeper technical understanding.

---

### 8. Learning GitHub Actions (Brent Laster)

- **Publisher:** O'Reilly
- **Level:** Intermediate
- **Why read it:** Focused entirely on GitHub Actions. Covers workflow syntax, custom actions, reusable workflows, security, and CI/CD patterns. Essential if you're using GitHub for DevOps.

---

### 9. The DevOps Handbook (Gene Kim, Jez Humble, Patrick Debois, John Willis)

- **Publisher:** IT Revolution Press
- **Level:** Intermediate
- **Why read it:** Not Git-specific, but covers the practices and principles that make version control essential — CI/CD, infrastructure as code, deployment pipelines. Puts Git in the context of modern software delivery.

---

### 10. Continuous Delivery (Jez Humble & David Farley)

- **Publisher:** Addison-Wesley
- **Level:** Intermediate → Advanced
- **Why read it:** The foundational text on deployment pipelines, trunk-based development, and release engineering. Understanding these concepts explains *why* Git workflows are designed the way they are.

---

## Reading Order Recommendation

```
Beginner:
  1. Pro Git (Ch. 1-3)           → Learn the commands
  2. Think Like (a) Git          → Build the mental model
  3. GitHub Open Source Guides    → Learn collaboration

Intermediate:
  4. Pro Git (Ch. 5, 7)          → Advanced workflows
  5. Git from the Bottom Up      → Understand internals
  6. Learning GitHub Actions     → CI/CD automation

Advanced:
  7. Pro Git (Ch. 10)            → Git internals deep dive
  8. The DevOps Handbook         → Broader context
  9. Continuous Delivery         → Deployment pipelines
```

---

> **Start here:** Just read **Pro Git** — it's free, comprehensive, and written by a GitHub co-founder. If you finish chapters 1-5, you'll know more about Git than most professional developers.
