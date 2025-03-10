---   
title: Git Command Basics to Advance Notes 
author: ajaytekam   
date: 2023-07-25 16:10:00 +0530   
img_path: /assets/posts/20230725/   
categories: [DevOps, Git, VCS, Github]    
tags: [yaml, git, vcs, Github, DevOpsTools]  
image:
  path: git0.png 
  alt: Git Command Basics to Advance Notes 
---    

## Contents 

- [Repository Initialization](#repository-initialization)  
- [Pull and fetch](#pull-and-fetch)  
- [Git Log and Previous Commits](#git-log-and-previous-commits)   
- [Git Branch and Merge](#git-branch-and-merge)  
- [Temporary Commits : Git Stash](#temporary-commits)
- [Stage and Snapshots](#stage-and-snapshots)  
- [Inspact and Compare](#inspact-and-compare)  
- [Git pull and Fetch](#git-pull-and-fetch)
- [Git Reset](#git-reset)   
- [Git PAN key Management](#git-pan-key-management)         
- [Cherry Picking](#cherry-picking)     
- [Rebasing](#rebasing)     
- [Merge Conflicts](#merge-conflicts)   
- [Git Branching Stratagies](#git-branching-stratagies)     
- [Pull Requests](#pull-requests)    
- [Upstream](#upstream)    
- [Tools for Git](#tools-for-git)   

------------------

## Repository Initialization  

* If you are using git for the first time then you have to setup email and usernames    

```    
git config --global user.name "JohnDoe"
git config --global user.email "johndoe@gmail.com"  
```    

* Create repository 

```sh 
mkdir myrepo
```

* Initialize git repo 

```sh   
git init
```  

* Stage files 

```sh   
// stage single file 
git add MyFile  
// stage all files 
git add .
```  

* Commit changes 

```sh   
git commit -m "Message"
```  

* Set remote Repository url 

```sh
git remote add origin https://gitserver.com/user/myrepo.git
```

* Change remote  

```sh
git remote set-url origin https://gitserver.com/user/myrepo.git
```  

* See remote url   

```sh
git remote -v 
``` 

* Remove origin URL 

```sh 
git remote remove origin 
``` 

* If you have multiple branches you can push all of them at once  

```sh   
git push -all origin  
```  

* Push only selected branch or newly created branch   

```sh  
git push origin devBranch  
```     

## Pull and Fetch  

* fetch command  

```sh
git fetch origin main
```  

Git fetch download latest commits from remote repo, this command is safe to run at any time since it never changes any of your local branches under refs/heads, so you can continuely work on your code and after that to merge with fetched code run    

```sh   
git merge FETCH_HEAD  
```  

* pull command  

```sh   
git pull origin main
```  

pull command download latest commit from the remote repo, it is basically combination of  `git fetch origin main` and `git merge FETCH_HEAD`.    

## Git Log and Previous Commits   

Shows all the prevous commits 

```sh    
git log
```  

Shows all the previous commits in short forms  

```sh   
git log --oneline  
```  

* Now to checkout particular commit just type `git checkout <commit_id>`    
* And to jump out main branch type `git switch -` 

## Rolling Back 

* When you staged a file/files `git add file`, then you have done some modification, and you need to revert back then use :

```sh   
git restore staged <file/files_name>  
```   

* When you have commited the modification and you want to revert back then you can use 

First run `git log --oneline`, then select copy the commit id/hash and type below command   

```sh  
git revert <commit-hash>
```   

This will basically create a new HEAD commit where you can work.   


## Git Branch and Merge   

* A branch is a lightweight movable pointer that represents an independent line of development within a repository.   
* It allows you to work on different features, bug fixes, or improvements without affecting the main codebase.  
* Each branch in Git has its own commit history, which means you can make changes in one branch without affecting other branches until you decide to merge those changes.   

List all branch  

```sh 
git branch
``` 

Create new Branch 

```sh   
git branch -c Branch_name
// or 
git branch Branch_name
```  

Switch into another branch 

```sh   
git checkout branch_name 
// or 
git switch branch_name
```  

Creating a new branch and checking out at the sametime  

```sh   
git checkout -b branch_name 
```  

__Merging branch :__  

* git merge preserves the commit history of both branches, resulting in a "merge commit" that combines the changes from the source branch into the target branch.  
* Once the merge is completed, the changes from the source branch will be integrated into the target branch, and the commit history will reflect the merge commit.  

```sh  
git merge branch 
```  

* Reset git merge 

first run command 

```sh   
git reflog
```  

Now find the HEAD before merge command, for example "HEAD@{2}", now use the below command 
 
```sh  
git reset --hard "HEAD@{2}"
```  

##  Temporary Commits 

`git stash` command is used to temporarily  store the un-commited work on a temporary commit or stash. So you can pull the latest commit from remote repo and after that you can restore the stash with the latest commit

> Note : the git stash command does not store un-staged files, it does not include changes that are un-staged.  

To Create a stash 

```sh   
git stash 
```   

To list all the stash 

```sh  
git stash 
// or 
git stash list
```  

Add stashed files into repo after pull 

```sh  
git stash pop   
```  

The above command also remove the stash from stack (stash stack). To appy the stash without removing from the stack   

```sh   
git stash apply 
```   

Shows the changes in detials  

```sh  
git stash show 
```  

similar to above command  

```sh  
git stash show -p 
``` 

Remove the single entry from the stack 

```sh  
git stash drop <stash_id>   
```  

Example 

```sh  
git stash drop stash@{1}
```  

## Stage and Snapshots 

show modifications

```sh  
git status 
``` 

Stage a file 

```sh   
git add file_name 
``` 

Unstage a file  

```sh   
git reset file_name 
``` 

Unstage all files and revert back to last commit

```sh   
git reset --hard HEAD
``` 

See what is changed but not staged

```sh  
git diff
``` 

Show what is changed but not yet commited  

```sh  
git diff --staged
```  

## Inspact and Compare  

Show commit history 

```sh   
git log
```  

Show difference between branch commit

```sh   
git log branchA..branchB
```

Show commits between two branches  

```sh  
git log --follow file_name
``` 

Difference between two branch 

```sh  
git diff branchA..branchB
```

## Git Pull and Fetch

Pull and merge all remote repo 

```sh  
git pull [origin]
```  

Delete Remote Repository 

```sh  
git push origin --delete branchName
```  


color autoconfig

```sh  
git config --global color.ui auto  
```  

## Git Reset

* `git reset` command used to move the current branch's HEAD to a specific commit, effectively undoing or redoing changes and modifying the state of the repository.  

* What is HEAD ? 
    - HEAD is a special pointer or reference that points to the most recent commit on the current branch.   
    - It represents the tip of the branch, which is the commit where the branch is currently pointing.  
    - When a commit is made then HEAD pointer moves forward to the newly created commit, pointing to the latest state of the branch.  

__Git reset use cases :__  

* Undo the commit 
* Goto previous commit
* Undo merge
* Undo rebase 

__Types of reset :__   

* Soft Reset : 
    - Moves the branch's HEAD to the specified commit.  
    -   Leaves the changes from the commits between the current HEAD and the target commit in the staging area (index).  
    - Leaves the changes from the commits between the target commit and the original branch tip in the working directory.   
    - Useful for "undoing" a commit while keeping the changes staged for the next commit.   
    - `git reset --soft <commit_sha>`   
* Mixed Reset : 
    - This is the default mode of git reset.
    - Moves the branch's HEAD to the specified commit.
    - Resets the staging area (index) to match the target commit, effectively removing all changes from the staging area.
    - Leaves the changes from the commits between the target commit and the original branch tip in the working directory.
    - Useful for "undoing" a commit and un-staging the changes.
* Hard Reset : 
    - Moves the branch's HEAD to the specified commit.  
    - Resets the staging area (index) to match the target commit, effectively removing all changes from the staging area.  
    - Discards all changes from the commits between the target commit and the original branch tip in the working directory.  
    - Irreversibly discards changes, so use this form with caution.  
    - Useful for completely undoing changes and making the working directory match a specific commit.   

> Note :  Be careful when using the `--hard` option, as it can lead to data loss if used incorrectly. It's recommended to create a backup or use Git's reflog (git reflog) to recover lost changes in case of accidental data loss.   

Example : 

* Soft reset : Undo the commit, change the HEAD into provide SHA commit and keep the changes in staged area.  

Get the commit logs using `git log` or `git log --oneline` for short sha one code, now reset the commit 

```sh  
git reset --soft <commit_SHA>
```   

* Hard reset : Undo the commit, change the HEAD into provide SHA commit and compeletely delete all the changes.   

```sh  
git reset --hard <commit_SHA>
```   

* Mixed reset : Undo the commit, change the HEAD into provide SHA commit and keep changes in unstaged area.  

```sh  
git reset --hard <commit_SHA>
```   

## Git PAN key Management   

__Create PAT :__

* Click on Profile Icon on upper-right corner and goto `settings > Developer Settings > Parsenal access tokens`.   
* Set Name, expirance date and select the necessary permission then generate personal access token.      

__Using PAT with git repos :__ 

* Push temporarily :  

Just push the repo and paste the PAT in username field and leave password field blank.  

```sh   
git push -u origin main 
username: $YOUR_PERSONAL_ACCESS_TOKEN
password: <leave_this_empty_and_hit_enter>  
```  

__Cached Token :__

You can cache the token for 15 minutes by running below command 

```sh  
git config credential.helper cache   
```  

and then push the repo

```sh  
git push -u origin main 
username: $YOUR_PERSONAL_ACCESS_TOKEN
password: <leave_this_empty_and_hit_enter>  
```  

Now you can push the repos for 15 minutes without providing PAT 

__Store credentials :__ 

You can store credentials permanently so you don't have to provide the PAT again 

```sh  
git config credential.helper store  
```  

Push the repo  

```sh  
git push -u origin main 
username: $YOUR_PERSONAL_ACCESS_TOKEN
password: <leave_this_empty_and_hit_enter>  
```  

This will stary parmenent and you can use it to push any repo . 

## Cherry Picking

`git cherry-pick` command is used to pick a commit from a branch and applying it to current working HEAD.   

* Use cases:
    - Undo changes when you accidently made commit to the wrong branch.   
    - Bug fixes: chery-pick the bug fix commit directly into the main branch
    - Undoing changes and restoring lost commits  

Example : 

For example there is a feature branch and main branch, now cherry-picking from feature branch to main branch  

```
git cherry-pick <feature_branch_SHA1_signature> 
```   

Above command also commit all the changes from feature branch to main branch. So just to copy the changes from feature branch into commit branch use the flag `--no-commit`  

```sh  
git cherry-pick <feature_branch_SHA1_signature> --no-commit   
```  

Change/edit the commit message 

```sh  
git cherry-pick <feature_branch_SHA1_signature> --edit 
```  

## Rebasing  

* `git rebase` integrate changes from one branch into another by moving or applying a series of commits on top of another branch's commit history.   
* It is an alternative to merging and is commonly used to keep a cleaner and more linear commit history.  
* git rebase command takes all commits from the target branch (which you want to rebase with current branch, example feature_branch) and apply all the commits of that branch into current branch.  

Example: Suppose we are in master branch and want to rebase feature branch into master branch 

```sh  
git rebase feature 
```  

* Reset git rebase command 

first run command 

```sh  
git reflog
```  

Now find the HEAD before rebase command, for example "HEAD@{2}", now use the below command 
 
```sh  
git reset --hard "HEAD@{2}"
```  

> Notes on rebase Safe Practices: Do not rebase on commits that you've already pushed/shared on a remote repository, instead use for cleaning up your local commit history before merging  it into a shared team branch.   

## Merge Conflicts

Merge conflicts occur when Git attempts to integrate changes from two different branches, but it finds conflicting modifications in the same part of a file or in related parts of multiple files. Essentially, Git doesn't know which version of the conflicting changes to apply automatically, and it requires manual intervention from the developer to resolve the conflicts.    


Merge conflicts commonly occurs during these scenarios:    

* git merge
* git rebase
* git pull 

When a merge conflict occurs, Git will identify the conflicting files and mark the conflicting lines within those files with special markers indicating the different versions.  

Example 

```sh  
#!/bin/bash 

<<<<<<< HEAD
echo "Hello world"
=======
echo "Hello world Again"
>>>>>>> feature-branch  
```  

* The `<<<<<<< HEAD` line marks the beginning of the conflicting changes from your current branch (also known as the "HEAD" branch). 
* The `=======` line separates the conflicting changes from the other branch you are merging. 
* The `>>>>>>> feature-branch` line indicates the end of the conflicting changes from the branch you're merging, where "feature-branch" is the name of the branch you are merging.  
* Git couldn't automatically resolve the changes and needs your manual intervention to decide which changes to keep and how to combine them.  
* You'll need to edit the file, remove the conflict markers, and choose the correct content that should be included in the final version.  
* Once you've resolved all the conflicts in the file, you can save it, stage it with git add, and complete the merge by creating a new merge commit with git commit.
* It's important to be careful while resolving merge conflicts and to ensure that the final result is what you intend it to be, as incorrect resolutions can lead to code errors or unintended behavior. Also, when working in a team, communication is essential to avoid conflicts and to resolve them efficiently when they do occur.

## Git Branching Stratagies   

* A branching strategy is a set of rules that software development teams adopt when writing, merging and deploying code when using a version control system.  
* Such a strategy is necessary as it helps keep repositories organized to avoid errors in the application when multiple developers are working simultaneously and are all adding their changes at the same time.  

__Example of Branching Stratagies :__  

* Mainline Development : 
    - Few branches 
    - Relatively small commits
    - High-quality testing and QA standards

![](git1.png)  

* State, Release and Feature Branches 
    - Branches enhances structures and workflows 
    - different types of branches 
    - fulfill different types of jobs  

![](git2.png)    

- Long running branches 
    - exists through the complete lifetime of the project 
    - often, they mirror "stages" in your dev life cycle 
    - common convention: no direct commits
    - example: 'develop branch'  

![](git3.png)   

- Short lived branches  
    - for new features, bug fixes, refactoring, experiments... 
    - will be deleted after integration (merge/rebase)  

![](git4.png)   

__Example of Branching Stratagies :__  

* Github Flow    
    - Very simple, very lean, only one long running branch ("main") + feature branch  

![](git5.png)   

* GitFlow  
    - More structure, more rules    
    - Long-running: "main" + "develop"    
    - Short-lived: features, releases, hotfixes  

![](git6.png)  

## Pull Requests    

Git pull request is a feature used to propose changes from one branch to another, typically from a feature branch to a main branch.     

1. Creating a Feature Branch      
2. Making Changes                 
3. Pushing the Feature Branch     
4. Creating the Pull Request      
5. Reviewing the changes          
6. Addressing the Feadback       
7. Merge or Close                
 
## Upstream  

* In the context of github "upstream" or "upstream URL" refers to the URL of the original repository from which a fork was created.   
* When you fork a repository on GitHub, you create a copy of that repository under your GitHub account. The repository you forked from becomes the "upstream repository," and your forked copy is the "forked repository."
* The upstream URL points back to the original repository. It allows you to track and synchronize changes made in the upstream repository with your fork. This is particularly useful when you want to keep your fork up to date with the latest changes from the original repository.

Steps :  

1. Fork the repository   
2. Clone your fork into local system   
3. Add upstrem url using `git remote add upstream ORIGINAL_REPO_URL`   
4. Verify upstream url by `git remote -v`   
5. Now fetch the latest changes from the original repository into your local fork `git fetch upstream`.    

## Tools for Git

* __lazygit :__ simple terminal UI for git commands.  

Github repo : https://github.com/jesseduffield/lazygit  

![](git8.gif)  

* __tig :__ text-mode interface for Git.    

![](git7.png)    

* __git-graph :__ A command line tool to visualize Git history graphs.   

Github repo : https://github.com/mlange-42/git-graph   

![](https://user-images.githubusercontent.com/44003176/103466403-36a81780-4d45-11eb-90cc-167d210d7a52.png)     

* __vim-fugitive :__ Vim plugin for Git.  

Github repo : https://github.com/tpope/vim-fugitive

