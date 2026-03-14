# 01 — Introduction and Setup

## What is Version Control?

Version control is a system that records changes to files over time so you can recall specific versions later. Think of it as a **time machine for your code** — you can go back to any point in your project's history, see what changed, who changed it, and why.

Without version control:
- You end up with files like `project_final_v2_ACTUAL_FINAL.zip`
- Collaborating with teammates means emailing zip files back and forth
- One wrong deletion and your work is gone forever

With version control:
- Every change is tracked with a message explaining why
- Multiple people can work on the same project simultaneously
- You can experiment freely because you can always roll back

---

## Git vs GitHub — Not the Same Thing

This is the most common confusion for beginners. They are two completely different things.

| | Git | GitHub |
|---|---|---|
| What it is | A tool (software) | A website (service) |
| Runs on | Your local machine | The internet (cloud) |
| Purpose | Track changes in your files | Host and share Git repositories |
| Made by | Linus Torvalds (2005) | GitHub Inc. (2008), now Microsoft |
| Needs internet? | No | Yes |
| Alternatives | — | GitLab, Bitbucket, Gitea |

**Analogy:** Git is like Microsoft Word (the tool). GitHub is like Google Drive (where you store and share your Word files).

---

## Why Git Specifically?

There were version control systems before Git (SVN, CVS, Perforce). Git won because:

- **Distributed** — every developer has a full copy of the entire history. No single point of failure.
- **Fast** — almost all operations are local (no network needed)
- **Branching is cheap** — creating a branch takes milliseconds, not minutes
- **Industry standard** — used by Linux kernel, Google, Microsoft, Meta, and virtually every software company

### Git vs SVN (quick comparison)

| | Git | SVN |
|---|---|---|
| Architecture | Distributed | Centralized |
| Branching | Fast, lightweight | Slow, heavy |
| Offline work | Full history available | Very limited |
| Used today | Dominant | Legacy systems mostly |

---

## Installing Git

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install git -y
git --version
```

### Linux (RHEL/CentOS/Fedora)
```bash
sudo dnf install git -y
git --version
```

### macOS
```bash
# Option 1: via Homebrew (recommended)
brew install git

# Option 2: Xcode Command Line Tools (installs automatically when you run git)
git --version
```

### Windows
Download from https://git-scm.com/download/win — use **Git for Windows** which includes Git Bash.

---

## First-Time Configuration

After installing Git, you must tell it who you are. This information gets attached to every commit you make.

```bash
# Set your name (use your real name or GitHub username)
git config --global user.name "Your Name"

# Set your email (use the same email as your GitHub account)
git config --global user.email "you@example.com"

# Set your default editor (choose one)
git config --global core.editor "vim"        # Vim
git config --global core.editor "nano"       # Nano (easier for beginners)
git config --global core.editor "code --wait" # VS Code

# Set default branch name to 'main' (modern standard, was 'master' before)
git config --global init.defaultBranch main

# Make Git output colorful (highly recommended)
git config --global color.ui auto
```

### Verify your config
```bash
git config --list
# Or check a specific value
git config user.name
git config user.email
```

### Where is the config stored?

| Scope | Flag | File location | Applies to |
|---|---|---|---|
| System | `--system` | `/etc/gitconfig` | All users on the machine |
| Global | `--global` | `~/.gitconfig` | Your user account |
| Local | `--local` | `.git/config` in the repo | That specific repo only |

Local overrides global, global overrides system.

```bash
# View your global config file directly
cat ~/.gitconfig
```

---

## SSH Key Setup for GitHub

HTTPS and SSH are two ways to authenticate with GitHub. SSH is preferred because:
- No need to type your password every time you push
- More secure (uses cryptographic keys)
- Required once, then transparent forever

### Step 1 — Generate an SSH key pair
```bash
# Generate a new ED25519 key (modern, recommended)
ssh-keygen -t ed25519 -C "you@example.com"

# When prompted for a file path, press Enter to accept the default:
# /home/yourname/.ssh/id_ed25519

# When prompted for a passphrase, either:
# - Press Enter for no passphrase (less secure, more convenient)
# - Type a passphrase (more secure, you'll need it occasionally)
```

This creates two files:
- `~/.ssh/id_ed25519` — your **private key** (never share this)
- `~/.ssh/id_ed25519.pub` — your **public key** (this goes on GitHub)

### Step 2 — Add your public key to GitHub
```bash
# Print your public key
cat ~/.ssh/id_ed25519.pub
# Copy the entire output (starts with 'ssh-ed25519 ...')
```

Then on GitHub:
1. Go to **Settings → SSH and GPG keys → New SSH key**
2. Give it a title (e.g., "My Laptop")
3. Paste your public key
4. Click **Add SSH key**

### Step 3 — Test the connection
```bash
ssh -T git@github.com
# You should see: Hi username! You've successfully authenticated...
```

### Optional: SSH config for multiple accounts
```bash
# Create/edit ~/.ssh/config
nano ~/.ssh/config
```

```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

# If you have a work GitHub account:
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
```

---

## Cloning your first repository

```bash
# Clone using SSH (recommended if SSH key is set up)
git clone git@github.com:username/repo-name.git

# Clone using HTTPS
git clone https://github.com/username/repo-name.git

# Clone into a custom folder name
git clone git@github.com:username/repo-name.git my-project
```

---

## Quick Sanity Check — Your setup is ready if:

```bash
git --version          # shows a version number
git config user.name   # shows your name
git config user.email  # shows your email
ssh -T git@github.com  # shows "Hi username! You've successfully authenticated"
```

---

## Common Mistakes at This Stage

| Mistake | Fix |
|---|---|
| Using GitHub email different from `git config` email | Commits won't link to your GitHub profile — make them match |
| Sharing your private key (`id_ed25519`) | Only share `id_ed25519.pub` — the `.pub` file |
| Skipping SSH setup and using HTTPS | You'll be asked for a password on every push |
| Not setting `init.defaultBranch main` | You'll get branches named `master` which is outdated |
