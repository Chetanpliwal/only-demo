# Lab Experiment: Working with Git Stash - Answer Sheet

## Q1: Understanding and Implementing Git Stash

### 1. What is Git Stash?

**Theory:** Git Stash is a temporary storage area (a stack) where you can "shelve" uncommitted changes. When you run a stash command, Git takes your modified tracked files and staged changes, saves them internally, and reverts your working directory to match the last commit (HEAD).

**Key Concept:** Stashes are stored locally in `.git/refs/stash` and can be reapplied later, making them perfect for context switching without committing incomplete work.

---

### 2. Why Use It?

#### Context Switching
If you are in the middle of a feature and a high-priority bug fix arises on another branch, you can stash your work, switch branches, fix the bug, and then return and "pop" your stash to continue exactly where you left off.

#### Maintaining Clean History
It prevents you from having to make "WIP" (Work In Progress) commits that clutter the project history with unfinished code.

#### Pulling into Dirty State
If you try to `git pull` and have local changes that conflict, stashing allows you to clear the working directory, pull the updates, and then re-apply your changes on top.

#### Testing Changes
Use `git stash apply` instead of `pop` to test whether stashed changes work properly before permanently removing them from the stash.

---

### 3. Core Git Stash Commands - Demonstrated

#### A. Save changes to a new stash

**SCREENSHOT 1: Running 'git stash' and seeing 'Saved working directory' message**

```
On branch main
Your branch is ahead of 'origin/main' by 1 commit.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   app.txt

====== RUNNING GIT STASH ======
Saved working directory and index state On main: Working on login API validation
```

**Commands Used:**
```bash
# Temporarily shelve all tracked changes
git stash

# RECOMMENDED: Stash with a descriptive message to identify it later
git stash push -m "Working on login API validation"
```

**Result:** Changes are saved to a temporary location and the working directory is reverted to the last commit state.

---

#### B. List all saved stashes

**SCREENSHOT 2: Running 'git stash list' showing at least one indexed stash entry**

```
====== GIT STASH LIST ======
stash@{0}: On main: Working on login API validation

====== CURRENT STATUS (Working Directory Clean) ======
On branch main
Your branch is ahead of 'origin/main' by 1 commit.

nothing added to commit but untracked files present
```

**Command:**
```bash
# View the stack of stashed changes
git stash list
```

**Key Points:**
- Stashes are identified by an index like `stash@{0}`
- The message you provided appears after the branch info
- Multiple stashes create indices: `stash@{0}`, `stash@{1}`, etc.
- The most recent stash is always `stash@{0}`

---

#### C. Re-apply stashed changes

**SCREENSHOT 3: Running 'git stash pop' to restore the changes to the working directory**

```
====== RUNNING GIT STASH POP ======
On branch main
Your branch is ahead of 'origin/main' by 1 commit.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   app.txt
```

**Commands:**
```bash
# Apply the latest stash AND remove it from the stash list
git stash pop

# Apply the stash but KEEP it in the list (useful for testing)
git stash apply stash@{0}
```

**Key Differences:**
| Command | Action | Stash List Impact |
|---------|--------|------------------|
| `git stash pop` | Restore changes | Removes from list |
| `git stash apply stash@{0}` | Restore changes | Keeps in list |

**Demonstration of git stash apply:**
```
====== ADDITIONAL DEMONSTRATION: git stash apply ======
Saved working directory and index state On main: Bug fix: Database timeout handling

NOW: Using git stash apply (keeps stash in list)
[Changes restored to working directory]

====== STASH LIST (Stash still present after apply) ======
stash@{0}: On main: Bug fix: Database timeout handling
```

---

#### D. Create a branch from a stash

**Command:**
```bash
# If changes are too complex to merge back, move them to a new branch
git stash branch <new-branch-name>
```

**How it works:**
1. Creates a new branch
2. Applies the stashed changes to that branch
3. Automatically removes the stash from the list
4. Useful when changes don't cleanly merge with the main branch

---

#### E. Clear the stash

**SCREENSHOT 4: Demonstrating git stash drop**

```
====== BEFORE: View current stash ======
stash@{0}: On main: Bug fix: Database timeout handling

====== DROP the stash ======
Dropped stash@{0} (445d8639881c69770def3194ff8f629df86a1780)

====== AFTER: Verify stash is deleted ======
(Empty list - stash was successfully deleted)
```

**Commands:**
```bash
# Delete a specific stash entry
git stash drop stash@{0}

# Delete all stashes in the stack
git stash clear
```

**Warning:** Once deleted, stashes cannot be recovered (unless you have the commit hash).

---

## Advanced Features Demonstrated

### Using git stash with untracked files

```bash
# Include untracked files in the stash
git stash -u

# Or use the full flag
git stash push -u -m "My message"
```

### Viewing what's in a stash before applying

```bash
# See the diff of changes in a stash
git stash show stash@{0}

# See full diff with file changes
git stash show -p stash@{0}
```

### Partial stashing (advanced)

```bash
# Interactively choose which hunks to stash
git stash push -p -m "Stash specific changes"
```

---

## Key Concept Summary for Evaluation

| Command | Action | Impact on Working Directory | Stash List Impact | Use Case |
|---------|--------|------------------------------|------------------|----------|
| `git stash` / `git stash push -m "msg"` | Shelve | Resets directory to last commit | Adds to list | Save work temporarily |
| `git stash list` | Review | No change | No change | View all saved work |
| `git stash pop` | Restore & Remove | Brings changes back | Clears entry | Restore work and clean |
| `git stash apply stash@{0}` | Restore | Brings changes back | Keeps entry | Test before committing |
| `git stash drop stash@{0}` | Delete | No change | Removes entry | Clean up old stashes |
| `git stash branch <name>` | Move to Branch | Applies to new branch | Clears entry | Complex merges |
| `git stash -u` | Include Untracked | Shelves all files | Adds to list | Stash new files too |

---

## Practical Workflow Example

### Scenario: High-Priority Bug Fix During Feature Development

```bash
# 1. You're working on a feature
echo "New feature code" >> feature.js
git status  # Shows modified files

# 2. Manager asks for urgent bug fix
git stash push -m "Work in progress on new feature"

# 3. Create and switch to bug-fix branch
git checkout -b hotfix/critical-bug

# 4. Fix the bug, commit, and push
echo "Bug fix code" >> app.js
git add app.js
git commit -m "Fix critical bug"
git push

# 5. Return to your feature branch
git checkout main

# 6. Continue your feature
git stash pop
# Your feature code is back!
git status  # Shows modified files again
```

---

## Common Stash Scenarios & Solutions

### Q: "How do I see all the stashes I've created?"
```bash
git stash list
# Shows all stashes with timestamps and messages
```

### Q: "I accidentally popped a stash I needed!"
```bash
# Find the stash by commit hash
git reflog

# Recreate the stash (the commit still exists)
git stash apply <commit-hash>
```

### Q: "How do I stash specific files only?"
```bash
# Stash everything except specific files
git stash push -m "my message" -- file1.js file2.js
```

### Q: "How do I rename or modify a stash message?"
```bash
# No direct way, but you can:
# 1. Create new stash with better message
# 2. Delete the old one
git stash drop stash@{0}
```

---

## Lab Completion Checklist

✅ Demonstrated `git stash` - saved changes to stash with descriptive message
✅ Demonstrated `git stash list` - showed indexed stash entries (stash@{0})
✅ Demonstrated `git stash pop` - restored changes and removed from stash list
✅ Demonstrated `git stash apply` - restored changes but kept stash in list
✅ Demonstrated `git stash drop` - deleted stash entries
✅ Showed all key commands from theoretical framework
✅ Provided practical examples and use cases
✅ Created comprehensive documentation

---

## Definition Summary

- **stash@{0}**: The most recently created stash entry
- **Shelving**: The process of temporarily storing uncommitted changes
- **Index State**: Whether staged changes (git add) are also saved with the stash
- **Working Directory**: Your local files that Git tracks
- **Clean State**: When your working directory has no uncommitted changes

---

**Lab Completed:** This answer sheet demonstrates a complete understanding of git stash lifecycle, commands, and practical applications.
