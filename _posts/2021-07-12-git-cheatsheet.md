---
title: How to Git
excerpt: >-
  Useful git commands and tricks. Gathered from my experience and from the ESCAPE School. Will continue adding.
authors: C. García-Quirós
date: '2021-07-12'
layout: post
---


## Table of Contents
1. [Configurations](#configurations)
2. [Create Clone Repo](#create-clone-repo)
3. [git status](#git-status)
4. [git add](#git-add)
5. [git commit](#git-commit)
6. [git log](#git-log)
7. [git diff](#git-diff)
8. [git push](#git-push)
9. [git checkout](#git-checkout)
10. [git fetch merge pull](#git-fetch-merge-pull)
11. [git rebase](#git-rebase)
12. [Branches](#branches)
13. [Multiple Remotes](#multiple-remotes)
14. [Submodules](#submodules)
15. [Undo git merge squash](#undo-git-merge-squash)
16. [Tags](#tags)
17. [Animate Git History](#animate-git-history)

Useful links:
- [PRO git book (webpage)](https://git-scm.com/book/en/v2)<br/>
- [Missing semester](https://missing.csail.mit.edu/2020/version-control/)<br/>
- [Escape School (repo)](https://github.com/escape2020/school2021/tree/main/git) [(lecture)](https://escape2020.github.io/school2021/posts/clase04/)


## Configurations

- Tell git who are you, for the log history

    - `git config --global usern.name "Cecilio García"`
    - `git config --global user.email "ceciliogarciaquiros@gmail.com"`
<br/><br/>
- Modify the editor used by git for the commit message

    - `git config --global core.editor "code --wait"` Use visual studio code
    - `git config --global core.editor nano`        Use nano
<br/><br/>
- Change name of default branch to "main"
    - `git config --global init.defaultBranch main`
<br/><br/>
- Change style of merge conflicts.
    - `git config --global merge.conflicstyle diff3`  Show difference between HEAD, common ancestor and incoming branch
<br/><br/>
- Local files not to be under version control. You don't need to skip them in `git add`.
    - Add folder name, files to .gitignore, e.g.:
        - build/
        - *.so
        - \_\_pycache\_\_/
    - To ignore for all the repositories
        - `git config --global core.excludesfiles $HOME/.gitignore` put in $HOME/.gitignore those files to be globally ignored. E.g.: \_\_MACOSX, .DS_STORE, *.swp, *~
<br/><br/>
- `git config --global pull.rebase true`  Set `git pull --rebase` as default when doing `git pull`

## Create Clone Repo

1. Create repo locally:
    - `git init` (inside a folder with repo name. It creates the folder .git)
    - To link a new repo to github, create in github a new repo and locally do <br/>
    `git remote add origin git@github.com:Ceciliogq/school2021.git`  (choose the repo name)
<br/><br/>
2. Clone existing repository:
    - `git clone git@github.com:Ceciliogq/school2021.git` (having setup ssh in github first)
    - `git clone repo_address custom_repo_name`   (change repo name when downloading)
    - `git clone repo_address . `  (download all the files in the repo, without the source folder)
    - `git clone -b branch_name repo_address`   (clone only one branch)
<br/><br/>
3. With https you need to enter username and password every time
<br/><br/>
4. `rm -rf .git` remove all trace of git.

## git status

See the current status of the local repo: modified files, (un-)staged files, untracked files, etc.

- `git status -s` will show a reduced version, with one line per file. To the left, a coloured letter will tell the status of the file.

## git add

- `git add filename` Add one file to the staged area
- `git add . `  Add all the files, tracked and untracked in the current folder
- `git add -u`  Add all the modified files which are tracked
- `git add -p(--ptach)`    It allows you to choose wich junks of code wants to add. E.g. if a file contains changes that should belong to different commits.
- `git add foldername ':!*.h5'`  add folder except some kind of files
- `git add _`  Something when there is a merge conflict???

- `git reset filename`  Remove file from the staged area. (You added the wrong file e.g.)
- A alternative is doing `git restore --staged filename`
<br/>

- `git mv oldfilename newfilename`  rename a file and put it in the staged area.
- `git rm filename`  remove a file and put the change in the staged area.
- Both need to do commit afterwards

- Move local changes to another branch.
I did some changes but then realized they were in the incorrect branch. Stash the local changes, move to the correct branch, and pop them.
    - `git stash`
    - `git checkout >correct-branch>`
    - `git stash pop`

## git commit

- `git commit -m "Commit message" filename`  Will commit the added changes in filename. This will only in your local repo not yet on the internet. Without -m will promt you to write a log message. If the message contains '#number', it will link to the issue 'number'
- Modify message of last commit
   - `git commit --amend -m "New message"`  This changes the hash number
   - For pushed commits use `git push --force remotename branchname`

## git log

See history of commits

- `git log -N`  Will the last N commits
- `git log commit1..commit2`  (without spaces). Will show the commits from 1 to 2 including 2. 1 must be previous to 2.
- `git log --oneline`  Only one line per commit
- `git log --name-status`  show the name of the files changed
- `git log -p`  show the code changes of each commit
- _Pending git log --name-status origin/master.._
- `git shortlog`  Show the contributors and their commits
    - `-s`only shows the number of commits
    - `-n` ordered by number of commits
    - `-e`  add the email
    - Can be combined as `-sne`
- `git log --all --decorate --graph`  shows a drawing of the tree structure of commits
   - `--decorate` shows special tags of commits like HEAD
   - `--all` shows all the refs in .git/refs as commit
   - `--graph` show drawing of commit relations

## git diff

See difference between working directory and staging area.

- `git diff --staged`  See what is going to be committed
- `git diff branch1 branch2` or `file1 file2` difference between branches, files, commits, etc.
- Save git diff in file and see in color
- `git diff --color source_file > output.txt`
- `less -R output.txt`

## git push

Publish the commits on the internet repo

- `git push` default repo and branch (origin/master). First time `git push -u origin branch`
- `git push reponame branchname`
- `git push -f(--force)` force the commits to be published.
- `git push origin --delete branchname`  delete the branch remotely

## git checkout

- `git checkout commit_has` load specific commit
- Switching between branches has now changed to `git switch`
   - `git switch branchname`
   - `git switch -c newbranch` create new branch and change to it
   - `git switch -c newbranch upstream/master`  create new branch from another branch
- `git checkout hash -- filename`  get back a commit for a specific file
- This has now been replaced by `git restore`
    - `git restore filename`  get last commit for a file, discard all the changes
    - `git restore --source=hash or branch filename`  restore a file to a specific commit or branch
- `git checkout -b new_branch remote/branchname`  clone remote branch
- `git branch new_branch` create new branch pointing to current commit

## git fetch merge pull

- `git fetch`  Bring changes from remote to a local staging area
- `git merge`  Merge previous changes to default remote/branch (remote, branch can be changed). The commits has are not changed, it add a "merge commit" and the rest are merged in cronological order
- `git merge branch_to_be_merged`  This will include the changes in "branch_to_be_merged" into the current branch where you are "checkout"
- `git merge --squas`  Squash all the commits in one single merge commit
- `git pull` = `git fetch` + `git merge default/default`
- `git pull remote branch` pull specific fork and branch


## git rebase

For rewriting the git history: change commit order, drop/edit commits, merge multiple commits into one.

- `git rebase branch1 branch2` it will put the commits of branch2 on top of branch1
- `git rebase remote/branch`  it takes the history of remote/branch and will add the local changes (commits) on top
- `git pull --rebase` = `git fetch` & previous point
- `git rebase -i commit_hash`  modify a commit interactively


## Branches

- `git branch` see current branches
<br/>
- Get branch from merge request
    - `git fetch upstream merge-requests/id/head:new_branch`
    - `git checkout new_branch`
    - id is the number of the MR and new_branch the name I give to the new branch
<br/>
- Delete branches
    - `git branch -d localBranchName`  delete local branch
    - `git push origin --delete remoteBranchName`  delete branch remotely
<br/>
- Discard local changes and bring remote branch
    - `git fetch --all`
    - `git reset --hard origin/master`
<br/>
- Rename Branches
    - `git branch -m <oldname> <newname>`
    - To rename the current branch `git branch -m <newname>`
    - To push the local branch and reset the upstream one:
      `git push origin -u <newname>`
    - Delete the old remote branch `git push origin --delete <oldname>`

## Multiple Remotes

When you want to contribute to an existing project you typically fork that repo into your username and clone your fork. That is your 'origin', but you can other repos and call the whaterver you want. It is common practice to name the second one as 'upstream'.

- To link your local repo to the upstream one:
`git remote add upstream url_of_desired_repo`

You can fetch chages from upstream, merge, switch branches and push changes to it.

- `git remote -v` to see the forks available
- `git remote rm upstream`  to remove one fork
- Move branch from one repo to another:    
   - `git remote add remoteRepo git@git/https...`
   - `git checkout branch-to-copy`
   - `git push reomteRepo`

## Submodules

- `git submodule add url path`  Add reference to another repository at a certain commit
- `git clone url --recursive`   Clone also the submodules
- `git submodule update --init --recursive` Update submodules (e.g. if changed on the remote)

## Undo git merge squash
- `git fetch upstream`  (lscsoft/master)
- `git show commit1_hash`  This is the commit where my branch diverged from upstream/master after the merge
- `git revert commit1_hash`  This will make a series of commit to undo the merge commit
- `git rebase -i commit2_hash`  This is the previous to commit1
- Delete the line of my big merge into master
- `git merge upstream/master`
- Solve conflicts if any
- `git add`, `git commit`, `git push -f origin/review-branch`


## Tags
Tag a specific commit, e.g. for released versions.
There are two types of tags:
- Lightweight: only points to a commit
- Annotated: include more info like a messages and the tagger's info

- `git tag tagname` Lightweight
- `git tag -a tagname -m "Version v.1"` Annotated
- `git tag -a tagname commit`  Make annotated tag of old commit

- `git tag`  to see the list of tags
- `git tag -l "v1.8*"`  see the list of tags matching a pattern

- `git show tagname`  See info about a tag

- `git push origin tagname`  publish tag
- `git push origin --tags`  publish all tags
- `git push origin --follow-tags` publish only annotated tags

- `git tag -d tagname`  delete tag in local repo
- `git push origin --delete tagname`  delete tag in remote
- `git push origin :refs/tags/tagname`  same than previous

- `git checkout tagname`  switch to a tag
- `git checkout -b new_branch tagname`  create new branch from tag


## Animate Git history

With [Gource](https://gource.io/) we can do beautiful animations of the history of a git repository.

The video below shows the history of the [LALSuite repo](https://git.ligo.org/lscsoft/lalsuite) from 2018 to 2021. The specific command run to produce the video was

```bash
gource --start-date "2018-01-01" --hide dirnames,filenames --seconds-per-day 0.05 --auto-skip-seconds 1 -1280x720 -o - | ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i - -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 gource.mp4.
```

Enjoy the firecommits!

<iframe width="560" height="315" src="https://www.youtube.com/embed/5GWVeVw0-Do" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
