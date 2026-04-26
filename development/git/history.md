# Rewriting Git history

Git history is immutable by design, but there are legitimate reasons to rewrite it: removing accidentally committed secrets, purging large binary files, or eliminating a directory that was never meant to be tracked.

**Any history rewrite changes commit SHAs.** Everyone who has cloned the repo must re-clone or hard-reset after a force-push.

## git filter-repo (recommended)

`git filter-repo` is the modern replacement for `git filter-branch`. It is faster, safer, and ships as a single Python script.

```bash
pip install git-filter-repo
```

### Remove a folder from all history

```bash
git filter-repo --path some/folder --invert-paths
```

`--invert-paths` means "keep everything except this path". Run from the repo root.

### Remove a single file from all history

```bash
git filter-repo --path secrets.env --invert-paths
```

### Remove by glob pattern

```bash
git filter-repo --path-glob '*.zip' --invert-paths
```

### Recommended workflow (fresh clone)

`git filter-repo` refuses to run on a repo that still has its original remote configured, unless you pass `--force`. Working on a mirror clone avoids that:

```bash
git clone --mirror git@github.com:you/repo.git repo-mirror
cd repo-mirror
git filter-repo --path some/folder --invert-paths
git push --force
```

After pushing, all collaborators must re-clone:

```bash
git clone git@github.com:you/repo.git
```

Or reset an existing clone:

```bash
git fetch origin
git reset --hard origin/main
```

## Stop tracking a folder (without rewriting history)

If keeping the old history is acceptable and you only want to stop tracking a folder going forward:

```bash
echo "some/folder/" >> .gitignore
git rm -r --cached some/folder
git commit -m "stop tracking some/folder"
```

The folder remains on disk and in old commits, but Git will no longer track changes to it.

## BFG Repo Cleaner (alternative)

[BFG](https://rtyley.github.io/bfg-repo-cleaner/) is a Java-based alternative, simpler for the specific case of removing large files or secrets:

```bash
# Remove all files named id_rsa anywhere in history
java -jar bfg.jar --delete-files id_rsa repo-mirror.git

# Remove files larger than 50MB
java -jar bfg.jar --strip-blobs-bigger-than 50M repo-mirror.git

# After BFG, expire and clean
cd repo-mirror.git
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force
```

BFG is faster than `git filter-repo` for large repos but less flexible — it cannot filter by path prefix or apply arbitrary transformations.

## Notes on GitHub / GitLab caching

Even after a force-push, the hosting platform may cache old objects for a while. If you removed sensitive data:

- **GitHub**: contact support to run a git garbage collection on their end
- **GitLab**: `Repository → Housekeeping` in project settings, or contact support for object pruning

## Resources

- [git-filter-repo on GitHub](https://github.com/newren/git-filter-repo)
- [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [GitHub: removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
