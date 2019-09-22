---
layout: post
title: "Git Stash Urgent Fix Not Found"
description: "Somework that you should not use git stash"
img: git-stash-urgent-fix-not-found.jpg
tags: [Git]
---

Git version control is a popular version control for modern developer. As a ROR developer I am frequently use git stash. Sometime when an urgent fix come out I stash my changed and switch to other branch in order to fix it. Then I start to feel that git stash is dangerous as it only store code temporarily and easier to clean by git stash clear. So I found a solution and I write in this post here.

<!-- ad -->

### Bad stash

* Stash when we don't want to commit our half working to repo.
* Stash for a quick save when some urgent request for other branch in the middle of feature implement.

### Good stash
* Move changed from one branch to another branch. I personally recommend this is the right way to use git stash.
Why not git stash when urgent fix
* Accidentally do stash clear
* It doesn't stay in separate branch. Git stash of every branch will all go stash
Difficult to manage. Stash only manage by stash pop, stash apply and stash clear
Messy stash

### Solution Here
Before you switch to other branch please do the following command
```
git add . --all
git commit -m 'stash commit'
```
Both command above will add every file you changed include untracked and deleted files. And put it to a commit in your branch that commit name as '**stash commit**'

Then switch to other branch by git checkout. After finished on the other branch switch back to your branch and do:
```
git reset HEAD^
```
This git reset will undo one of your commit and revert it back to files changed, untracked and deleted files.

So now you can go on with your branch safely.
