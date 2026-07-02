---
title: Git
tags: [tools, version-control, devops]
related: [Claude Code, Async Programming]
sources: [Git.md, Git/Git.md, Git/mastering Git.md, Git/Advanced Git Techniques.md, Git/Advanced Git Tips and Tricks.md, GitLab2.md]
updated: 2026-04-12
---

# Git

Distributed version control system. The entire repository (history, branches, tags) lives locally — nearly every operation is local and fast.

## Core model

Git stores **snapshots**, not diffs. Each commit is a snapshot of the entire working tree at that point in time.

![Snapshots over time](../../raw/Git_files/media/image1.png)

### Three areas

| Area | Description |
|---|---|
| **Working Directory** | Files you're currently editing |
| **Staging Area (Index)** | Files staged for the next commit (`git add`) |
| **.git directory** | The repository — all commits, branches, objects |

![Working directory, staging, repository](../../raw/Git_files/media/image2.png)

### Object model

Every object in Git is content-addressed (SHA-1 hash):

| Object | Description |
|---|---|
| **Blob** | File contents |
| **Tree** | Directory — maps names to blobs/trees |
| **Commit** | Snapshot + parent(s) + author + message |
| **Tag** | Named reference to a commit |
| **Branch** | Movable pointer to a commit |
| **HEAD** | Points to the current branch (or commit in detached state) |

---

## Essential commands

### Setup
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Start a repo
```bash
git init                          # new repo in current dir
git clone <url>                   # clone remote repo
git clone <url> <local-name>      # clone with custom folder name
```

### Daily workflow
```bash
git status                        # what's changed
git add <file>                    # stage file
git add .                         # stage all changes
git commit -m "message"           # commit staged changes
git push                          # push to remote
git pull                          # fetch + merge from remote
```

### Branching
```bash
git branch                        # list branches
git branch <name>                 # create branch
git checkout <name>               # switch to branch
git checkout -b <name>            # create and switch
git merge <branch>                # merge branch into current
git rebase <branch>               # rebase current onto branch
git branch -d <name>              # delete branch
```

### Inspection
```bash
git log                           # commit history
git log --pretty=oneline          # compact log
git log --graph --oneline         # visual branch graph
git diff                          # unstaged changes
git diff --staged                 # staged vs last commit
git diff --word-diff              # word-level diff
git blame <file>                  # who changed each line
```

### Undoing
```bash
git stash                         # save working changes, revert to HEAD
git stash pop                     # restore stashed changes
git commit --amend                # modify last commit (before push)
git revert <sha>                  # new commit that undoes a commit
git reset --hard <sha>            # discard commits (destructive)
git cherry-pick <sha>             # apply a specific commit to current branch
git rm --cached file.txt          # remove from index only (untrack, keep on disk)
git mv old.txt new.txt            # rename/move (stages the rename)
```

### Reset modes

`git reset` moves the branch tip and optionally updates the index and working area:

| Flag | Branch | Index | Working Area |
|---|---|---|---|
| `--soft` | Moved | Unchanged | Unchanged |
| `--mixed` (default) | Moved | Reset | Unchanged |
| `--hard` | Moved | Reset | Reset |

```bash
git reset HEAD file.txt         # unstage a file (keep working copy)
git reset --hard HEAD           # discard all uncommitted changes
git reset --hard HEAD~1         # undo last commit + wipe changes
git restore file.txt            # discard working changes (modern)
git restore --staged file.txt   # unstage without losing changes
git checkout HEAD file.txt      # reset file in both working area and index
```

---

## Stash

Stash saves your uncommitted changes and reverts the working directory to the last commit. Useful when switching branches mid-work.

```bash
git stash                         # save changes
git stash list                    # see all stashes
git stash pop                     # restore most recent stash
git stash apply stash@{2}         # restore a specific stash
git stash drop stash@{0}          # delete a stash
```

---

## Squash commits

Collapse multiple commits into one before merging (cleaner history).

```bash
# Squash last 2 commits interactively
git rebase --interactive HEAD~2
# In the editor: change 'pick' to 'squash' (or 's') on commits to merge
# Edit and save the combined commit message
```

---

## Tags

Mark specific commits (e.g. releases).

```bash
git tag v1.0                      # lightweight tag
git tag -a v1.0 -m "Release 1.0" # annotated tag (preferred)
git push origin --tags            # push all tags
```

---

## Configuration levels

Config is evaluated from most specific to least — repository overrides user, user overrides system.

| Level | Scope | File location (Linux/Mac) |
|---|---|---|
| `--local` | This repository | `.git/config` |
| `--global` | Your user account | `~/.gitconfig` |
| `--system` | All users on machine | `/usr/local/etc/gitconfig` |

```bash
git config --global user.name "Ken"
git config --local core.ignorecase false
git config --global --add core.pager "less -RFX"   # quit pager at end of output
```

---

## Hooks

Shell scripts that run automatically at Git lifecycle events. Stored in `.git/hooks/`.

| Hook | When |
|---|---|
| `pre-commit` | Before commit is created — run linters, tests |
| `commit-msg` | Validate/enforce commit message format |
| `post-commit` | After commit — notifications |
| `pre-push` | Before push — run full test suite |
| `pre-receive` | Server-side: before accepting push |
| `post-receive` | Server-side: after push — trigger CI/CD |

**Example: enforce commit message template**
```bash
# .git/hooks/commit-msg
#!/bin/sh
grep -qE "^\[JIRA-[0-9]+\]" "$1" || {
  echo "Commit message must start with [JIRA-NNN]"
  exit 1
}
```

---

## Submodules

Embed one repository inside another. Useful for shared libraries.

```bash
git submodule add <url> <path>    # add submodule
git submodule update --init       # initialise after clone
git submodule update --remote     # pull latest from submodule remote
```

---

## GitLab CI/CD

GitLab uses a `.gitlab-ci.yml` file to define pipelines.

### Pipeline structure: Jobs → Stages → Pipeline

```
A.) Build
    i.  Install NPM Dependencies
    ii. Run ES-Linter
    iii. Run Code-Minifier
B.) Test
    i.  Run unit, functional, E2E tests
    ii. Compile application
C.) Deploy
    i.  Staging  → local dev server
    ii. Production → AWS EC2
```

### Example `.gitlab-ci.yml`

```yaml
image: node:10.5.0

stages:
  - build
  - test
  - deploy

before_script:
  - npm install

build-min-code:
  stage: build
  script:
    - npm run minifier

run-unit-test:
  stage: test
  script:
    - npm run test

deploy-staging:
  stage: deploy
  script:
    - npm run deploy-stage
  only:
    - develop

deploy-production:
  stage: deploy
  script:
    - npm run deploy-prod
  only:
    - master
```

### CI/CD concepts

| Term | Meaning |
|---|---|
| **CI (Continuous Integration)** | Automatically build and test on every push |
| **CD (Continuous Delivery)** | Auto-deploy to staging; manual gate to production |
| **CD (Continuous Deployment)** | Auto-deploy all the way to production |
| **Pipeline** | Full sequence: Code → Build → Test → Deploy |
| **Runner** | Agent that executes jobs |

CI servers: Jenkins, GitLab CI, CircleCI, TeamCity, Travis CI, GitHub Actions.

---

## Useful log formats

```bash
# Pretty one-liner with graph
git log --pretty='%Cred%h%Creset |%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset %C(cyan)[%an]%Creset' --graph

# Last 5 entries in log.md style
grep "^## \[" log.md | tail -5
```

---

## Windows tooling

- **Git for Windows (msysGit)** — `https://gitforwindows.org/`
- **posh-git** — PowerShell integration with branch status prompt
  ```powershell
  git clone https://github.com/dahlbyk/posh-git.git
  ```

---

## See also
- [[Claude-Code]] — Claude Code integrates with git (worktrees, autonomous PRs)
- [[SOLID-Principles]] — version control enforces single-responsibility at the commit level
