Git Branching & Merging Cheat Sheet
SEO Title: Git Branching & Merging Cheat Sheet: Commands, Workflows & Best Practices
Meta Description: Master Git branching and merging with essential commands, practical workflows, conflict resolution tips, and common gotchas for junior developers.
Target Audience: CS Students / Junior Developers
Category: DevOps
1. Quick-Reference Command Matrix
Action / Workflow
Command
Description
Create & Switch Branch
git switch -c <branch-name>
Creates a new branch and switches to it (modern syntax).
Alternative Create/Switch
git checkout -b <branch-name>
Classic syntax to create and checkout a new branch.
List Local Branches
git branch
Lists all local branches (* marks current branch).
List All Branches
git branch -a
Lists both local and remote-tracking branches.
Switch Branch
git switch <branch-name>
Switches HEAD to the specified existing branch.
Rename Branch
git branch -m <old-name> <new-name>
Renames a branch locally.
Delete Merged Branch
git branch -d <branch-name>
Safely deletes a branch only if fully merged.
Force Delete Branch
git branch -D <branch-name>
Forcefully deletes a branch regardless of merge status.
Merge Branch
git merge <feature-branch>
Merges specified branch into the currently checked-out branch.
Merge No-Fast-Forward
git merge --no-ff <feature-branch>
Forces creation of a merge commit even if fast-forward is possible.
Squash Merge
git merge --squash <feature-branch>
Combines all feature commits into a single staged change set.
Abort In-Progress Merge
git merge --abort
Cancels an ongoing merge and restores working tree to pre-merge state.
Visual Commit Graph
git log --graph --oneline --all
Displays an ASCII visual graph of all branch histories and merges.
Show Merge Base
git merge-base <branch1> <branch2>
Finds the common ancestor commit of two branches.
Stash Working Changes
git stash push -m "wip feature"
Temporarily shelves uncommitted changes to allow safe switching.
Apply Stash
git stash pop
Reapplies the most recent stashed changes and removes from stash list.

2. Practical, Step-by-Step Code Snippets
Snippet 1: Standard Feature Branch Workflow
# 1. Ensure local main branch is clean and up to date

git switch main

git pull origin main

# 2. Create and switch to a dedicated feature branch

git switch -c feature/user-authentication

# 3. Work on code, stage changes, and create meaningful commits

git add src/auth/login.py tests/test_login.py

git commit -m "feat(auth): implement JWT token generation and login handler"

# 4. Push feature branch to remote repository

git push -u origin feature/user-authentication

# 5. Integrate back into main once approved (Fast-Forward or Merge Commit)

git switch main

git pull origin main

git merge --no-ff feature/user-authentication -m "Merge branch 'feature/user-authentication' into main"

# 6. Push updated main and clean up feature branch

git push origin main

git branch -d feature/user-authentication

git push origin --delete feature/user-authentication
Snippet 2: Resolving a Merge Conflict Step-by-Step
h
Scenario: Merging 'feature/payment-gateway' into 'main' causes a conflict
git switch main
git merge feature/payment-gateway
Output: CONFLICT (content): Merge conflict in config/app.json
Automatic merge failed; fix conflicts and then commit the result.
1. Check which files are in conflict
git status
2. Inspect conflict markers inside config/app.json:
<<<<<<< HEAD (Current Branch / main)
"api_version": "v1.2"
=======
"api_version": "v2.0"
>>>>>>> feature/payment-gateway
3. Edit the file to the desired resolution, removing conflict markers
e.g., keep "api_version": "v2.0"
4. Stage the resolved files
git add config/app.json
5. Finalize the merge commit
git commit -m "chore: resolve merge conflict in config/app.json for v2 API"

### Snippet 3: Cleaning Branch History with Squash Merge

```bash

# Combine multiple messy/WIP commits from a branch into one atomic commit

git switch main

git pull origin main

# Perform squash merge (stages all changes without committing immediately)

git merge --squash feature/quick-fixes

# Inspect staged changes

git status

# Create a single clean, concise commit message

git commit -m "feat(ui): refine dashboard layout, fix navbar responsiveness, and update color tokens"

# Safely delete the feature branch

git branch -D feature/quick-fixes
3. Common Mistakes & Gotchas
Gotcha 1: The "Detached HEAD" State
Problem: Running git checkout <commit-hash> or git checkout origin/main detaches HEAD from a branch pointer. Any new commits made in this state are orphaned and will eventually be garbage-collected by Git when you switch away.
Solution: If you made commits while detached and want to keep them, immediately create a new branch from your current position before switching:# Save your detached commits to a new branch

git branch recovery-branch

git switch recovery-branch
Gotcha 2: Committing Accidental Changes Directly to main
Problem: You started coding and made several commits on main instead of creating a feature branch, but you haven't pushed to remote yet.
Solution: Move your new commits to a fresh branch and reset local main back to match origin/main:# 1. Create a new branch pointing to your current commits

git branch feature/my-new-work

# 2. Reset local main back to remote's state (keeps remote untouched)

git reset --hard origin/main

# 3. Switch to your feature branch to continue safely

git switch feature/my-new-work
Gotcha 3: Accidentally Deleting an Unmerged Branch or Commits
Problem: You ran git branch -D feature-x or git reset --hard and lost important unpushed code.
Solution: Git rarely deletes data immediately. Use git reflog to find the lost commit hash and restore it:# 1. View recent HEAD history and find the commit SHA before deletion

git reflog

# 2. Recreate branch from the recovered commit SHA (e.g., abc1234)

git checkout -b recovered-feature-x abc1234
4. Frequently Asked Questions (FAQ)
Q1: What is the key difference between git merge and git rebase?
Answer: git merge is a non-destructive operation that creates a new "merge commit" uniting two branch histories, preserving the exact chronological sequence and context of when branches existed. git rebase rewrites history by replaying your branch's commits on top of another branch's base tip, creating a linear history. Rule of thumb: Use git rebase for local cleanup before sharing; never rebase public or shared shared branches.
Q2: Why should teams use git merge --no-ff (No Fast-Forward)?
Answer: When main has not progressed since branching, a default merge simply moves the main pointer forward (Fast-Forward), losing the record that a group of commits belonged to a distinct feature. Using --no-ff forces Git to generate a dedicated merge commit, making it easy to revert the entire feature as a single unit and preserving group context in commit logs.
Q3: How do I safely undo a merge that was already completed and pushed?
Answer: If the merge commit is already pushed to a shared remote, use git revert -m 1 <merge-commit-hash>. The -m 1 flag tells Git to keep the parent on the main mainline branch and revert the changes introduced by the merged feature branch without rewriting shared repository history. If the merge was only done locally and not pushed, you can run git reset --hard HEAD~1 or git reset --hard ORIG_HEAD.

