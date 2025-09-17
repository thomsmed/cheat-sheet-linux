# Git - The stupid content tracker

Git Cheat sheet

## Common tasks

### Creating branches

```bash
# Create local branch tracking a remote branch
git checkout -b --track <branch> <remote>/<branch>
```

### Rebasing

```bash
# Pull and rebase on remote tracked branch
git pull --rebase
```

### View commit history

```bash
# Commits associated with a tag
git log --no-walk --tags

# Commits that touch a specific file
git log --follow <filename>

# Commits between two points in history
# Note: If <commit2> is on a link directly reachable from <commit1>, these two notations gives the same result.
git log <commit1>..<commit2> # Commits reachable from <commit2> but not from <commit1>.
git log <commit1>...<commit2> # Commits reachable from <commit1> and <commit2>, but not from their common merge-base.

# Only merge commits
git log --pretty=oneline --merges x.y.z...origin/develop

# Only non-merge commits
git log --pretty=oneline --no-merges x.y.z...origin/develop

# Only direct commits, no branch commits
git log --pretty=oneline --first-parent x.y.z...origin/develop

# Commits that touches a specific file
git log --follow <filename>

# Commits that touches a specific file/path, evaluating the whole git history
git log --all --full-history -- "**/thefile.*"
git log --all --full-history -p -- "**/thefile.*"  # -p will show the “patch”/diff introduced by a given commit

# Pretty log only merge commits
git log --pretty=oneline --merges x.y.z...origin/develop
```

### View diffs

```bash
# Diff summary between two points in history
# Note: If <commit2> is on a link directly reachable from <commit1>, these two notations gives the same result.
git diff --stat <commit1>..<commit2> # Commits reachable from <commit2> but not from <commit1>.
git diff --stat <commit1>...<commit2> # Commits reachable from <commit1> and <commit2>, but not from their common merge-base.

# Complete diff
git diff <commit1>...<commit2>
```

### Tags

```bash
# Delete local and remote tag
git tag --delete 2.1.1
git push origin :2.1.1 # Pushing an "empty" tag to the remote, effectively deleting the remote tag
```
