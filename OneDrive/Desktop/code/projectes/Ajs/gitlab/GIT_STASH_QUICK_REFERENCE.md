# Git Stash Quick Reference Cheat Sheet

## 📋 One-Line Commands

### Save Work
```bash
git stash                              # Save all changes
git stash push -m "Description"        # Save with message
git stash push -u -m "msg"            # Include untracked files
git stash push -p -m "msg"            # Interactive: choose which changes
```

### View Stashes
```bash
git stash list                         # Show all stashes
git stash show stash@{0}              # Show what's in stash@{0}
git stash show -p stash@{0}           # Show full diff of stash
```

### Restore Work
```bash
git stash pop                          # Restore latest + remove from list
git stash apply stash@{0}             # Restore latest + keep in list
git stash pop stash@{1}               # Restore specific stash + remove
```

### Delete Stashes
```bash
git stash drop stash@{0}              # Delete specific stash
git stash clear                        # Delete all stashes
```

### Advanced
```bash
git stash branch <branch-name>        # Create branch from stash
git stash list --all                  # Show all stashes across branches
```

---

## 🎯 When to Use What

| Situation | Command | Why |
|-----------|---------|-----|
| Need to switch branches quickly | `git stash push -m "msg"` | Clean working dir |
| Testing stashed changes | `git stash apply` | Keeps original intact |
| Done with changes, restore them | `git stash pop` | Clean and restore |
| Complex merge needed | `git stash branch <name>` | Avoids conflicts |
| Just saving work for later | `git stash push -u` | Includes all files |

---

## ⚡ Workflow Examples

### Example 1: Quick Context Switch
```bash
git stash push -m "Login feature WIP"
git checkout fix-branch
# ... make fixes ...
git checkout main
git stash pop
```

### Example 2: Test Before Committing
```bash
git stash apply stash@{0}
# Run tests
git stash pop              # If tests pass, restore permanently
# OR
git stash drop stash@{0}   # If tests fail, discard
```

### Example 3: Multiple Stashes
```bash
git stash push -m "Feature A"
git stash push -m "Feature B"
git stash list
# stash@{0}: Feature B
# stash@{1}: Feature A

git stash pop stash@{1}    # Pop Feature A first
```

---

## 🔍 Troubleshooting

| Problem | Solution |
|---------|----------|
| "No stash entries found" | Create changes first: `echo "" >> file.js` |
| "Your local changes conflict" | Run `git stash apply` with `--index` flag |
| Accidentally deleted stash | Check `git reflog` to find commit hash |
| Stash not working | Ensure files are tracked by git: `git add file` |

---

## 🛟 Emergency Commands

```bash
# Find recently deleted stashes
git reflog

# Recover deleted stash
git stash apply <commit-hash>

# Convert stash to permanent commit
git stash branch recovery-branch
```

---

## 📊 Stash Index Reference

```
stash@{0} = Most recent (newest)
stash@{1} = Second most recent
stash@{2} = Third most recent
...
```

When you pop/drop a stash, indices shift down (stash@{1} becomes stash@{0}).

---

## ✅ Best Practices

1. **Always use descriptive messages**: `git stash push -m "JWT validation for login"` not just `git stash`
2. **Review before popping**: `git stash show -p stash@{0}` before restoring
3. **Don't stash for long periods**: Git stash is meant for short-term only
4. **Use branches for long-lived work**: Stash is not a substitute for branches
5. **Test applied stashes**: Use `apply` before `pop` when uncertain
6. **Clean up old stashes**: Regular `git stash drop` prevents confusion

---

**Created by:** Lab Experiment - Git Stash  
**Status:** Complete ✅
