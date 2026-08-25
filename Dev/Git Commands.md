## Git Commands
*Reference for daily version control workflows and common interview questions*

### Setup & Configuration

##### `git init`
- Initializes a new Git repository in the current directory

##### `git clone <url>`
- Copies an existing remote repository (and its full history) to your local machine

##### `git config --global user.name "Name"`
##### `git config --global user.email "email@example.com"`
- Sets the identity used for commit authorship on this machine

### Basic Workflow

##### `git status`
- Shows changed, staged, and untracked files in the working directory

##### `git add <file>`
##### `git add .`
- Stages changes so they're included in the next commit (`.` stages everything in the current directory)

##### `git commit -m "message"`
- Records staged changes as a new commit in the repository history

##### `git log`
- Shows the commit history for the current branch
##### `git log --oneline --graph`
- Compact, one-line-per-commit view with a visual branch graph

##### `git diff`
- Shows unstaged changes between the working directory and the last commit
##### `git diff --staged`
- Shows staged changes that haven't been committed yet

### Branching

##### `git branch`
- Lists local branches; `git branch <name>` creates a new one without switching to it

##### `git checkout <branch>`
##### `git switch <branch>`
- Switches the working directory to a different branch

##### `git checkout -b <branch>`
##### `git switch -c <branch>`
- Creates a new branch and switches to it in one step

##### `git branch -d <branch>`
- Deletes a branch that's already merged (`-D` force-deletes an unmerged branch)

##### `git merge <branch>`
- Combines the specified branch's history into the current branch

##### `git rebase <branch>`
- Replays the current branch's commits on top of another branch, producing a linear history

### Remote Repositories

##### `git remote -v`
- Lists the remote repositories connected to the local repo and their URLs

##### `git fetch`
- Downloads commits, branches, and tags from the remote without merging them into local branches

##### `git pull`
- Runs `git fetch` and then immediately merges the remote branch into the current branch
##### `git pull --rebase`
- Same as `git pull`, but rebases local commits on top of the fetched changes instead of merging

##### `git push`
- Uploads local commits on the current branch to the remote repository
##### `git push -u origin <branch>`
- Pushes a branch and sets it to track the remote branch, so future `push`/`pull` need no arguments

### Undoing Changes

##### `git restore <file>`
- Discards uncommitted changes in the working directory for a file, restoring it to the last commit

##### `git restore --staged <file>`
##### `git reset <file>`
- Unstages a file without discarding its changes

##### `git reset --soft <commit>`
- Moves the branch pointer to a commit, keeping all changes staged

##### `git reset --hard <commit>`
- Moves the branch pointer to a commit and discards all changes after it (destructive - use with care)

##### `git revert <commit>`
- Creates a new commit that undoes the changes from a specific commit, without rewriting history - safe for shared/pushed branches

### Stashing

##### `git stash`
- Temporarily saves uncommitted changes so you can switch context, without committing them

##### `git stash list`
- Shows all saved stashes

##### `git stash pop`
- Reapplies the most recent stash and removes it from the stash list

##### `git stash apply`
- Reapplies a stash but keeps it in the stash list

##### `git stash drop`
- Deletes a stash without applying it

### Interview Essentials

##### `git cherry-pick <commit>`
- Applies a specific commit from one branch onto another, without merging the whole branch

##### `git rebase -i <commit>`
- Interactive rebase - lets you squash, reorder, edit, or drop commits before they land on another branch

##### `git bisect`
- Binary-searches commit history to find which commit introduced a bug

##### `git blame <file>`
- Shows who last modified each line of a file and in which commit

##### `git tag <name>`
- Marks a specific commit, typically used for releases (e.g. `v1.0.0`)

##### `git reflog`
- Shows a log of every position HEAD has pointed to, useful for recovering "lost" commits after a hard reset

##### `.gitignore`
- File listing patterns Git should never track (e.g. `node_modules/`, `.env`)

##### Common Interview Concepts

| Concept                     | Explanation                                                                                   |
| ----------------------------- | ------------------------------------------------------------------------------------------------ |
| `git fetch` vs `git pull`      | `fetch` downloads changes without merging; `pull` fetches and merges (or rebases) automatically |
| `git merge` vs `git rebase`    | `merge` preserves history with a merge commit; `rebase` rewrites history into a linear sequence  |
| `git reset` vs `git revert`    | `reset` rewrites history by moving the branch pointer; `revert` adds a new commit that undoes changes, safe for shared branches |
| HEAD                          | Pointer to the current commit/branch you're working on                                          |
| Detached HEAD                 | HEAD points directly to a commit instead of a branch - commits made here can be lost if not tagged/branched |
| Merge Conflict                | Occurs when Git can't automatically reconcile changes to the same lines - requires manual resolution |
| Fast-forward merge            | When the target branch has no divergent commits, so the branch pointer simply moves forward     |
| Squashing commits              | Combining multiple commits into one, usually via `git rebase -i` before merging a feature branch |
