# PART 2: EXERCISES SUMMARY

**Context:** The project maintains two primary branches: `master` (for testing features) and `production` (for live code).

### 1. Branch Selection for New Features

**Requirement:** Determine the appropriate base branch when initiating a new feature.

**Result:**: New features should be based on the production branch instead of the master branch.

Reason: The production branch contains the current stable version of the application running in the live environment. In contrast, the master branch is used for testing and may include unfinished or unstable features. Creating new feature branches from production helps avoid inheriting untested changes and provides a more stable starting point for development.

**Git Commands:**
```bash
git checkout production
git pull origin production
git checkout -b feature/new-feature-name
```

---

### 2. Bug Resolution in Unmerged Feature Branches

**Requirement:** Define the procedure for resolving bugs within a feature branch that has not yet been integrated into production.

**Result:** Since the bug is in a feature branch that has not been merged to production yet, I would first update my local feature branch from the remote feature branch, reproduce the bug, then fix it directly there. I would not merge production into the feature branch unless the bug is related to recent production changes or the branch specifically needs to be synced with production.

**Git Commands:**
```bash
git checkout feature/your-feature-branch
git pull origin feature/your-feature-branch

```
Fix the bug
```bash
git add .
git commit -m "fix: resolve bug in feature branch"
git push origin feature/your-feature-branch
```
---

### 3. Removal of Accidentally Merged Features from Production


**Requirement:** A feature branch, for example `feature/delete-user`, was accidentally merged into `production`.

The feature contains these commits:

```text
0492978 -- fc9348c -- k101100
````

After that, another valid commit `a1fsas8` was added on top of `production`.

Current history:

```text
A -- B -- 0492978 -- fc9348c -- k101100 -- a1fsas8  production
```

The goal is to remove the accidentally merged feature while preserving `a1fsas8`.


## Preferred Approach: `git revert`

I would use `git revert` instead of `git reset`.

The reason is that `production` is a shared branch. Using `reset` would rewrite history and could affect other developers who already pulled from `production`.

`git revert` is safer because it creates a new commit that reverses the changes without deleting existing commits from history.


## Git Commands

```bash
git checkout production
git pull origin production

git revert -n k101100 fc9348c 0492978
git commit -m "revert: remove accidental feature/delete-user from production"

git push origin production
```


## Explanation

```bash
git revert -n k101100 fc9348c 0492978
```

The `-n` or `--no-commit` option applies all reverse changes to the working tree and index without automatically creating a commit.

This allows us to:

* review the reverted changes first;
* combine all reverted changes into one clean revert commit.

The commits are reverted from newest to oldest:

```text
k101100 -> fc9348c -> 0492978
```

This order helps reduce conflicts when commits depend on each other.

After running only `git revert -n`, the commit tree does not change yet:

```text
A -- B -- 0492978 -- fc9348c -- k101100 -- a1fsas8  production
```

After running:

```bash
git commit -m "revert: remove accidental feature/delete-user from production"
```

the history becomes:

```text
A -- B -- 0492978 -- fc9348c -- k101100 -- a1fsas8 -- R  production
```

Where `R` is the new revert commit.

This means:

* the original commits still exist in Git history;
* `a1fsas8` is preserved;
* `R` undoes the feature changes;
* production history is not rewritten.



## Pros of `git revert`

* Does not rewrite commit history.
* Safe for shared branches like `production`.
* Does not affect other developers' local clones.
* Keeps the valid commit `a1fsas8`.



## Cons of `git revert`

The original commits still remain in Git history.

If those commits contain sensitive data such as passwords, API keys, or tokens, `git revert` alone is not enough because the sensitive data can still be viewed from Git history.

In that case, we would need to rotate the secret and clean the Git history using a history rewrite tool.



## Other Possible Approaches

### Revert Each Commit Separately

```bash
git revert k101100
git revert fc9348c
git revert 0492978
```

This creates multiple revert commits instead of one combined revert commit.


The tree would look like this:
```text
A -- B -- 0492978 -- fc9348c -- k101100 -- a1fsas8 -- R1 -- R2 -- R3  production
```

### Revert the Merge Commit

If the feature was merged through an actual merge commit:

```bash
git revert -m 1 <merge_commit_id>
```

`-m 1` tells Git to keep the production side and undo the merged feature changes.

Example history:

```text
A -- B -- M -- D  production
      \      ^
       \     valid later commit
        C1 -- C2 -- C3  feature/delete-user
```

After reverting the merge commit:

```text
A -- B -- M -- D -- R  production
```

Where `R` reverses the changes introduced by merge commit `M`.

### Reset + Cherry-pick

```bash
git reset --hard <commit-before-0492978>
git cherry-pick a1fsas8
git push --force-with-lease origin production
```

This rewrites shared production history, so I would avoid it unless the team explicitly agrees.


Explanation:

```bash
git reset --hard <commit-before-0492978>
```

Move local `production` back to the commit before the bad feature was introduced.

Before reset:

```text
A -- B -- 0492978 -- fc9348c -- k101100 -- a1fsas8  production
```

After resetting to `B`:

```text
A -- B  production
```

This removes the feature commits from the branch history, but it also removes `a1fsas8` from the branch.

Then:

```bash
git cherry-pick a1fsas8
```

Re-apply the valid commit on top of `B`.

The history becomes:

```text
A -- B -- a1fsas8'  production
```

`a1fsas8'` contains the same changes as `a1fsas8`, but it usually has a different commit hash because its parent changed.

Finally:

```bash
git push --force-with-lease origin production
```

Push the rewritten history to remote `production`.

`--force-with-lease` is safer than `--force` because it refuses to overwrite remote changes if someone else pushed new commits while we were working.

However, I would avoid this approach on `production` unless the team explicitly agrees, because it rewrites shared history and can affect other developers who already pulled the old production branch.

The main downside of revert is that the original bad commits still remain in history. If those commits contain sensitive data, revert is not enough. In that case, we need to rotate the secret and clean the Git history with a history rewrite tool.

