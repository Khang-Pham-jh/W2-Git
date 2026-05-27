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

**Result:** Before attempting to fix the bug, it is crucial to synchronize the local feature branch with the latest updates to isolate the issue. First, we pull any new changes from the remote feature branch (if collaborating). Second, we fetch and merge the latest code from the production branch. This ensures the bug is still reproducible on the most current codebase and prevents resolving a bug that might have been caused by outdated base code.

**Git Commands:**
```bash
git checkout feature/your-feature-branch
git pull origin feature/your-feature-branch

# Fetch and merge the latest production code to update the base
git fetch origin
git merge origin/production
```
Fix the bug
```bash
git add .
git commit -m "fix: resolve bug in feature description"

# Case: Modifying the most recent local commit
git commit --amend --no-edit
```

---

### 3. Removal of Accidentally Merged Features from Production

**Requirement:** Address a scenario where a feature (e.g., `feature/delete-user` with IDs `0492978`, `fc9348c`, `k101100`) was merged into production, followed by a subsequent commit (`a1fsas8`).

**Result:** To preserve the integrity of the newer commit (`a1fsas8`), a `reset` command is avoided. The established solution is to use **`git revert`** to create new commits that mathematically negate the changes of the accidental merge, thereby maintaining a clean and accurate history for the production branch.


```bash
# Reverting individual commits from the faulty feature
git revert -n k101100 fc9348c 0492978
git commit -m "revert: remove accidental merge of feature/delete-user"

# Case: Reverting a specific Merge Commit (example ID: m12345)
# git revert -m 1 m12345

git push origin production
```
