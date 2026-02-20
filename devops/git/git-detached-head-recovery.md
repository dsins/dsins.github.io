# Git: Detached HEAD Recovery & Lost Commit Rescue

> **Quick diagnosis:** `git status` says `HEAD detached at ...`? You're in detached mode. Start here.

---

## Mental Model: HEAD, main, origin/main

| Pointer | What it is |
|---|---|
| `HEAD` | Where you are right now. Normally points to a branch, not a commit. |
| `main` | Your local branch pointer. |
| `origin/main` | A read-only tracking reference - Git's local record of where the remote was at last fetch/pull/push. **Not a branch to work on.** |

**Detached HEAD** = HEAD points directly to a commit SHA instead of a branch name. You can commit in this state, but those commits are not attached to any branch - they'll appear "lost" after switching away.

---

## Common Causes

```bash
git checkout <commit-sha>          # inspecting a specific commit
git checkout origin/main           # common VS Code / IDE mistake
# VS Code: clicking a commit → "Checkout"
```

---

## Diagnose Fast (3 Commands)

```bash
# 1. See where all pointers are
git log --oneline --decorate --all

# 2. See what actually happened (reflog = black box recorder)
git reflog --date=local

# 3. Check if a commit belongs to any branch (empty output = dangling)
git branch --contains <sha>
```

**Detached state in log looks like:**
```
* 471cf7b (HEAD) my second commit
* 2b68751 my first commit
* 0b43628 (origin/main, main) ← branch pointers are back here
```

---

## Recovery Workflow

### Scenario
- `0b43628` - bad commit, already pushed to `origin/main`
- `2b68751`, `471cf7b` - good commits made in detached state (not on any branch)
- Goal: remove bad commit, keep good work, push clean history

---

### Step 1 - Anchor detached commits to a temp branch

Do this **immediately** before switching anywhere:

```bash
git switch -c wip/local
```

This permanently attaches your "lost" commits to a named branch. They are now safe from garbage collection.

---

### Step 2 - Return to main

```bash
git checkout main
```

---

### Step 3 - Move main back to the last good commit

```bash
# Find the last good commit
git log --oneline --decorate --all

# Reset to it
git reset --hard <good-commit-sha>
# Example: git reset --hard 999b3ab
```

---

### Step 4 - Remove the bad commit from remote

```bash
git push --force-with-lease origin main
```

> **`--force-with-lease` vs `--force`:** `--force-with-lease` refuses to overwrite remote changes you don't have locally. Always prefer it over plain `--force` when rewriting history.

---

### Step 5 - Cherry-pick your good commits onto main

```bash
git cherry-pick 2b68751 471cf7b
```

**What cherry-pick does:** Copies the diff introduced by each commit and applies it on top of the current branch as a new commit. The content is preserved; the SHA will change.

> Cherry-pick multiple commits in chronological order (oldest first).

---

### Step 6 - Push

```bash
git push origin main
```

---

### Step 7 - Verify

```bash
git log --oneline --decorate --graph --all --max-count=20
```

Healthy end state: `main` and `origin/main` both point to the same latest commit.

---

## Cleanup (Optional)

```bash
git branch -d wip/local        # delete temp branch if no longer needed
```

---

## Revert vs Reset - Choose Correctly

| | `git revert` | `git reset --hard` + `--force-with-lease` |
|---|---|---|
| **Use when** | Others may have pulled the bad commit | You're the only consumer, or team can re-sync |
| **History** | Adds a new "undo" commit - history stays intact | Rewrites history - bad commit removed |
| **Safe for teams** | Yes | No - others must `git pull --rebase` or reset |

```bash
# Option A: Revert (team-safe)
git revert 0b43628
git push origin main

# Option B: Reset + force-with-lease (clean history)
git reset --hard <good-sha>
git push --force-with-lease origin main
```

---

## Why Commits "Disappear"

- **`git log` shows the wrong history** - `git log` only follows the current HEAD chain. If you're on a different branch, you see that branch's history. Use `git log --all` or `git reflog` to see everything.
- **Push didn't include detached commits** - `git push origin main` pushes the `main` branch, not HEAD. If HEAD is detached and ahead of `main`, those commits don't go anywhere.

---

## Prevention

```bash
# To inspect a commit without detaching long-term, create a branch immediately
git switch -c temp/inspect <sha>

# Never do this to continue working
git checkout origin/main    # read-only tracking ref, not a working branch
```

**AI agent / automation rules:**
- Allow: `git status`, `git diff`, `git add <specific-file>`, `git log`
- Disallow: `git pull`, `git rebase`, `git merge`, `git reset --hard`, `git push`
- Avoid `git add .` unless you've reviewed all changes

---

## Quick Checklist

```bash
git status                                        # confirm detached HEAD
git log --oneline --decorate --all                # locate all pointers
git reflog --date=local                           # trace what happened
git switch -c wip/local                           # anchor detached commits
git checkout main
git reset --hard <good-sha>                       # move main back
git push --force-with-lease origin main           # clean up remote
git cherry-pick <sha1> <sha2>                     # reattach good commits
git push origin main
git log --oneline --decorate --graph --all        # verify
```
