# Git - The stupid content tracker

Git Cheat sheet

## Create local branch tracking a remote branch

```bash
git checkout -b --track <branch> <remote>/<branch>
```

## Pull and rebase on remote tracked branch

```bash
git pull --rebase
```

## Log only commits associated with a tag

```bash
git log --no-walk --tags
```

## Log git history between two points in history

### Notations

<commit1>..<commit2>: Commits reachable from <commit2> but not from <commit1>.
<commit1>...<commit2>:  Commits reachable from <commit1> and <commit2>, but not from their common merge-base.
> Note: If <commit2> is on a link directly reachable from <commit1>, these two notations gives the same result.

### Only merge commits

```bash
git log --pretty=oneline --merges x.y.z...origin/develop
```

### Only non-merge commits

```bash
git log --pretty=oneline --no-merges x.y.z...origin/develop
```

### Only direct commits, no branch commits

```bash
git log --pretty=oneline --first-parent x.y.z...origin/develop
```

## View the difference between two points in history

### Notations

<commit1>..<commit2>: Commits reachable from <commit2> but not from <commit1>.
<commit1>...<commit2>:  Commits reachable from <commit1> and <commit2>, but not from their common merge-base.
> Note: If <commit2> is on a link directly reachable from <commit1>, these two notations gives the same result.

### Diff summary

```bash
git diff --stat <commit1>...<commit2>
```

### Complete diff

```bash
git diff <commit1>...<commit2>
```
