### Git configuration
Set the name and email that will be attached to your commits and tags.
```bash
git config --global
user.name “Your Name”
user.email “you@example.
```
Enable some colorization of Git output.
```bash
git config --global
color.ui auto
```
___
### Starting a project
Create a new local repository in the current directory. If project name is provided, Git will create a new directory named project name and will initialize a repository inside it.
```bash
git init project name
```
Downloads a project with the entire history from the remote
repository.
```bash
git clone <project url>
```
Git set stream-up and logic work
```bash
git remote -v
```
___
### Day-to-day work
Displays the status of your working directory. Options include new, staged, and modified files. It will retrieve branch name, current commit identifier, and changes pending commit.
```bash
git status
```
Add a file to the staging area. Use. in place of the full file path to add all changed files from the current directory down into the directory tree
```bash
git add file
```
Show changes between working directory and staging area.
```bash
git diff file
```
Shows any changes between the staging area and the repository.
```bash
git diff --staged file
```
Discard changes in working directory. This operation is unrecoverable.
```bash
git checkout -- [file]
```
Revert some paths in the index (or the whole index) to their state in HEAD.
```bash
git reset -<path>--.]
```
Create a new commit from changes added to the staging area. The commit must have a message!
```bash
git commit -M "commit"
```
___
### Storing your work
Put current changes in your working directory into stash for later use.
```bash
git stash
```
Apply stored stash content into working directory, and clear stash.
```bash
git stash pop
```
Delete a specific stash from all your previous stashes.
```bash
git stash drop
```
___
### Git branching model
List all local branches in repository. With -a: show all branches (with remote).
```bash
git branch [-a]
```
Create new branch, referencing the current HEAD.
```bash
git branch [branch_name]
```
Apply commits of the current working branch and apply them to the HEAD of [branch] to make the history of your branch more linear.
```bash
git rebase [branch_name]
```
Switch working directory to the specified branch. With -b: Git will create the specified branch if it does not exist.
```bash
git checkout [-b] [branch_name]
```
Join specified [branch_name] branch into your current branch (the one you are on currently).
```bash
git merge [branch_name]
```
Remove selected branch, if it is already merged into any other. -D instead of -d forces deletion.
```bash
git branch -d [branch_name]
```
___
### Inspect history
List commit history of current branch. -n count limits list to last n commits.
```bash
git log [-n count]
```
An overview with reference labels and history graph. One
commit per line.
```bash
git log --oneline
--graph --decorate
```
List commits that are present on the current branch and not
merged into ref. A ref can be a branch name or a tag name.
```bash
git log ref-.
```
List commit that are present on ref and not merged into current
branch.
```bash
git log -.ref
```
List operations (e.g. checkouts or commits) made on local
repository.
```bash
git reflog
```
___
### Tagging commits
List all tags.
```bash
git tag
```
Create a tag reference named name for current commit. Add commit sha to tag a specific commit instead of current one.
```bash
git tag [name]
[commit sha]
```
Create a tag object named name for current commit.
```bash
git tag -a [name]
[commit sha]
```
Remove a tag from local repository.
```bash
git tag -d [name]
```
___
### Reverting changes :luc_refresh_ccw:
Switches the current branch to the target reference, leaving a difference as an uncommitted change. When --hard is used, all changes are discarded. It's easy to lose uncommitted changes with --hard.
```bash
git reset [--hard]
[target reference]
```
Create a new commit, reverting changes from the specified
commit. It generates an inversion of changes.
```bash
git revert [commit sha]
```
___
### Synchronizing repositories
Fetch changes from the remote, but not update tracking branches.
```bash
git fetch [remote]
```
Delete remote Refs that were removed from the remote repository.
```bash
git fetch --prune
[remote]
```
Fetch changes from the remote and merge current branch with its upstream.
```bash
git pull [remote]
```
Push local changes to the remote. Use --tags to push tags.
```bash
git push [--tags]
[remote]
```
Push local branch to remote repository. Set its copy as an upstream.
```bash
git push -u [remote]
[branch]
```

