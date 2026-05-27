# PART 2: EXERCISES SUMMARY

**Context:** The project maintains two primary branches: `master` (for testing features) and `production` (for live code).

### 1. Branch Selection for New Features

**Requirement:** Determine the appropriate base branch when initiating a new feature.

**Result:** New features are typically based on the **`master` branch** if it serves as the repository for the latest tested code. Alternatively, the **`production` branch** can be used to ensure the feature starts from the most stable project state. Standard workflows generally favor creating feature branches from the main development line.

**Git Commands:**
```bash
git checkout master
git pull origin master
git checkout -b feature/new-feature-name
```

---

### 2. Bug Resolution in Unmerged Feature Branches

**Requirement:** Define the procedure for resolving bugs within a feature branch that has not yet been integrated into production.

**Result:** Bug fixes are performed directly on the affected feature branch. If the bug is located in the most recent commit and has not been pushed to the remote repository, the `--amend` command is utilized. For bugs in older commits or if the branch has already been pushed, a new fix commit is required.

**Git Commands:**
```bash
git checkout master
git pull origin master
git checkout feature/login 
git add .
git commit -m "fix: resolve bug in feature description"

# Case: Modifying the most recent local commit
git commit --amend --no-edit
```

---

### 3. Removal of Accidentally Merged Features from Production

**Requirement:** Address a scenario where a feature (e.g., `feature/delete-user` with IDs `0492978`, `fc9348c`, `k101100`) was merged into production, followed by a subsequent commit (`a1fsas8`).

**Result:** To preserve the integrity of the newer commit (`a1fsas8`), a `reset` command is avoided. The established solution is to use **`git revert`** to create new commits that mathematically negate the changes of the accidental merge, thereby maintaining a clean and accurate history for the production branch.

**Git Commands:**
The reversal can be executed for each individual commit ID in reverse order or by targeting the specific merge commit ID.

```bash
# Reverting individual commits from the faulty feature
git revert k101100
git revert fc9348c
git revert 0492978

# Case: Reverting a specific Merge Commit (example ID: m12345)
# git revert -m 1 m12345
```
