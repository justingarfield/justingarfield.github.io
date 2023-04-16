# Git Cheat Sheet

## Get rid of all commit history (sub-module safe)

```shell
#!/bin/bash

# Note: This will only work in a GitHub context. `refs/remotes/origin/HEAD` is injected by GitHub's environment.
#       Do these steps manually if running locally
default_branch=`basename $(git symbolic-ref --short refs/remotes/origin/HEAD)`

git checkout --orphan tmp
git add -A				            # Add all files and commit them
git commit
git branch -D $default_branch		# Deletes the default branch
git branch -m $default_branch		# Rename the current branch to default
git push -f origin $default_branch	# Force push default branch to github
git gc --aggressive --prune=all		# remove the old files
```

```shell
git checkout --orphan tmp
git add -A
git commit -m "Initial commit"
git branch -D main
git branch -m main
git push -f origin main
git gc --aggressive --prune=all
```

## Delete all tags (local and remote)

```shell
git tag -d $(git tag -l)
git fetch
git push origin --delete $(git tag -l)
git tag -d $(git tag -l)
```
