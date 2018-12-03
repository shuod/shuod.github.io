+++
title = "Basics of the github workflow for  a single user"
description = "using github to control work progress"

tags = ["Github", "Development"]
date = "2018-11-20"
categories = ["Development", "Github"]

image = "image.jpg" # optional
toc = true # optional, When set to TRUE this parameter, table of contents appears in only this article.
+++

In an earlier blog article, I wrote down some git workflow involved to in making this github page site (https://shuod.github.io/post/first/). And here, I will briefly write down a memo of github workflow for a single user. It is similar to git or gitlab workflow but more proper for the single user scenario. I assume you had setup an account on "github.com" and also a new project on github.



​	The streamline of  github workflow is like this:

- Making a local copy of the master branch from the remote source;
- Checking out a new branch, work on the local copy and make any changes you like;
- Recording changes to the local repository;
- Pushing the modified local copy to the corresponding branch in the remote source;
- Making a pull request (PR) on the project's webpage interface;
- Reviewing the code/changes, and decide to merge the new branch into the master branch or not;
- Some common issues include:
  - Some default branches;
  - how to sync to the most update-to-date version  while you are editing your copy;
  - how to delete the file/folder in the "git add" or "git commit";

## 1 Making a local copy of the master branch 

First,  copy the remote source of the project to local by:

```bash
git clone http://your_username.github.io/new_project.git
```

## 2 Checking out a new branch to work on

Second, setup a new branch in the local copy by:

```bash
git checkout -b chapter2_branch

// or if you want to delete this branch, after switch to another branch then you do:
git -d chapter2_branch
```

## 3 Making some changes

## 4 Recording changes to the local repository

```bash
#1 adding files to the buffer:
git add .
or
git add readme1.md readme2.md
#2 committing changes to the local repository:
git commit -m "continue edit on chapter 2"
#3 check status:
git status
#4 see log and ID of each commit:
git log
```

## 5 Pushing changes to the remote repository

```bash
git push origin chapter2_branch
```

## 6 Submit a pull request (PR)

Open the webpage of the project on "github.com", you can see a new notice highlight in yellow telling us that there is a new changes and a green button called "Compare & pull request".

Simply click the "Compare & pull request" button, a new dialog will show up and you can record some comments in it. After that, click on "Greate pull request" to submit a pull request.

## 7 Merge the PR into the master branch

After submitting the PR, emails will be sent to the people in charge. Since this is for single user, you will receive the notice. Then there will be a merge option available on the webpage, and you can choose to merge it into master branch. In the teamwork scenario, leader will review the code change and choose to merge it into the master branch

## 8 Some common issues:

### 1 Some default branches:

Usually, for a team, we have 

- a master branch that keep the latest stable version;
- a develop branch that contains fresh codes: it is a separate branch that keep going and when a milestone is achieved, it will merge into the master;
- a hotfix branch that fix  the emergency bug: it pulls from master and merge into master;
- several feature branched that implement some new features: they stem out from the develop branch and merge back into the develop branch;



For blog, we usually use the pull request (PR) to provide comments function. For writing, we could setup branches for each chapter. 

### 2 How to sync to the most update-to-date version (e.g. the chapter2_branch changed while you are editing your copy);

Sometime, a team may have several members to edit on the same branch (say the chapter2_branch). Then after one user's PR has been merged into the master branch, the chapter2_branch has proceed a step forward. At this time, if others tried to push their change, they will have some error like:

```bash
error: failed to push some refs to '/path/to/repo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```



To fix this, we need to update our local chapter2_branch. Don't forget to first verify that you are in the correct branch. Then, we update the chapter2_branch by either "git pull":

```bash
git pull --rebase origin chapter2_branch
```

However, if codes from two team members have conflicts, which cannot be merged automatically, then errors will pop up:

```
CONFLICT (rename/add): Rename CSIT/C++_in_easy_steps_Mike/chp3.md->CSIT/C++_in_easy_steps_Mike/chp3 branch.md in CSIT/C++ineasystep/chp4. CSIT/C++_in_easy_steps_Mike/chp3 branch.md added in HEAD
Adding as CSIT/C++_in_easy_steps_Mike/chp3 branch.md~HEAD instead
error: Failed to merge in the changes.
Patch failed at 0001 CSIT/C++ineasystep/chp4
Use 'git am --show-current-patch' to see the failed patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

 then you have to manually resolve the conflict issues by looking into the <some-file>. After fixing those conflicts, you then could continue the update process:

```
git add <some-file>
git rebase --continue
```

Or if you want to abort this rebase process, then

```
git rebase --abort
```

to roll back to the time before "git pull" was executed. 

### 3. How to delete the file/folder in the "git add" or "git commit"

1. git add 

   if you made a mistake in "git add " and haven't commit it yet, then:

   ```
   #1 check the status;
   git status
   #2 option1: roll back directly to the last version;
   git reset HEAD
   
   or 
   
   #2 option 2: roll back to individual changes:
   git reset HEAD \path\filename
   
   
   ```

2. git commit

   if you made a mistake and you already committed:

```
   git log <node>
   git reset 
```



### Reference:

1. Understanding the GitHub flow https://guides.github.com/introduction/flow/
2. Git feature workflow https://gist.github.com/blackfalcon/8428401
3. An animation of git workflow https://learngitbranching.js.org/?demo
4. Work with the GitHub Workflow https://trailhead.salesforce.com/en/content/learn/modules/git-and-git-hub-basics/work-with-the-git-hub-workflow






