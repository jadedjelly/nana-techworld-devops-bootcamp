# Git for Beginners
1. [Local and remote Repos](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Local-and-remote-Repos)
2. [Installing GIT](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Installing-GIT)
3. [(Init)ializing a GIT repo](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#(Init)-ializing-a-GIT-repo)
4. [Git Log](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#git-log)
5. [Git Branches](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#git-branches)
    - [Creating a new Branch](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Creating-a-new-Branch)
6. [Git Branching & merging](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Git-Branching-&-merging)
    - [Merging](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Merging)
7. [Initialize Remote Repos](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Initialize-Remote-Repos)
    - [Pushing to remote repos](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Pushing-to-remote-repos)
    - [Merging branch into master remotely](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Merging-branch-into-master-remotely)
7. [Rebasing vs Merge vs Squash](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Rebasing-vs-Merge-vs-Squash)
8. [Interactive Rebase](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/03_git.md#Interactive-Rebase)
9. 

## Local and remote Repos

Git has 2x repo types:

- Local
    - This is on your laptop, ie local!
    - Has 3 active areas, and also the order they get pushed to (Working > staging > committed):
        - Working area:
            - Where your making changes
        - Staging area:
            - New changes, that will soon be pushed / committed to the remote repo
        - Committed Files:
            - Already committed files
    
    ![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled.png)
    
- Remote
    - in a github, bitbucket, etc
    - great for backups, incase your local gets corrupted

## Installing GIT

Windows, Mac, Linux - Follow instructions

## (Init)ializing a GIT repo

run

```bash
git init
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%201.png)

doing this, creates an empty local git repo

We have created a simple text file (on the mac), with a line “This is a beautiful story”

We will now save this to the repo

Lets check the status:

```bash
git status
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%202.png)

Lets see Step by step:

- **On branch main**
    - *a Branch is a main / master snapshot. When you want to add a feature (big or small, you create / spawn a new branch to encapsulate the changes)*
    - *Branches will be explained more, later on*
- **No commits yet**
    - *Storing changes in your local is called committing*
    - *with every commit, we save the current changes, and show the changes we made to it*
- **Untracked files:**
    - **story1.txt**
        - *These are the files Git knows of, however we haven’t told git what to do with them.*
        - *We haven't told it, we want to add it our local repo*
- nothing added to commit but untracked files present (use "git add" to track)

Before we can commit a file, we 1st need to add it to the “staging area”, we can do this by running

```bash
git add story1.txt
```

Now it’s time to commit our change. 

A commit records the change of a repo compared to its previous state.

In this case, we didn’t have any previous commits, so the addition of the file story1.txt is a change compared to its previous state.

In order to commit the files that are currently in the staging area, we run the below, with a useful message (-m) 

```bash
git commit -m "Added first story"
```

When you make a commit, it’s good practice to summarize the change that you just made, this way you can easily track down when you made certain changes, and your team mates also know what you’ve been doing

========================

NOTE: git add takes the modded file in your current working dir, and places it in the staging area - this is not the same as the committed area!!!!!!!

the running git commit -m “commit message” moves the file to the most current snapshot / committed area

## Git Log

To view the commits in your terminal, run the below:

```bash
git log
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%203.png)

Shows the information that you need to know about all the commits, such as:

- Commit hash
- Author name
- date
- commit message

## GIT Branches

Say your adding a new feature, or new devs are joining the team to add a new feature. Instead of the new devs working on the production code, you create a branch which has the snapshot of the prod code, but they can make changes/ commits without effecting production. 

Only when their entire project is done & tested, we want to get the new code into production

What we’re actually saying here, is that we don’t want to always work on the master branch, but keep our project versioned using branches. 

A **BRANCH** is basically a pointer, to the last commit. If your working on a new feature, you might work on a branch, called “feature/signup”. And only once that feature has been finished and entirely tested, we want to get all those changes in the master branch.

### Creating a new Branch

Lets create a new branch

```bash
git checkout -b sarah
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%204.png)

To see a list of all other branches

```bash
git branch
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%205.png)

The Branch we are working on, will be highlighted, and have an * beside it

In total, we’re made one initial commit, they both point to that commit.

As noted before, **BRANCHES** are actually nothing more than pointers to a certain commit. Lets say sarah adds a new story, and commits this to the Sarah branch, but not to the master branch. The master branch is now one commit, behind the sarah branch

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%206.png)

Let’s say the team is growing, and you just hired a new dev called max, and he wants to write the 3rd story. Max is currently still checked out on the master branch, but to keep things clean, Max also creates his own branch called Max. 

Max writes the third story

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%207.png)

and commits this to his Max branch.

The sarah and master branch don’t have this change yet, only **Max’s**

Below are some useful commands when working with branches:

```bash
#Create a new branch
git branch sarah

#Switch to an existing branch
#git checkout [name of the branch you want to switch to]
git checkout sarah

#create a new branch and immediatley swtch to it
git checkout -b [new branch name]

#to delete a branch
git branch -d max

#to list all the branches
git branch
```

You might see the reference “to HEAD” in quite a few places.

a “HEAD” is where you are right now in the git repo, the HEAD points to the last commit in the branch that your currently on. When you switch branches, the HEAD moves with you….

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%208.png)

Branches are named after the feature being added or bug being fixed (Kodekloud person uses names…)

### Git Branching & Merging

Merging is the process of merging new additions to the master branch.

In the YT tutorial, we created a new git repo (called YT_git_branching&merging_tutorial) created a simple txt file with 3 commits & visible history [git logs] (”hello world” > “hello worlds” > “hello worlds!”). Going over what we discussed earlier, we can branch from the main branch, as to not effect production code.

Branching allows working on production code, where we would add features or hotfixes to a copy of the production code, but not effect the prod. This allows us to write and test, before “MERGING”

Below is a screenshot of the file history

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%209.png)

We create a new branch, this is usually named after what you are working on, for simplicity, we call it feature1.

when we run the git log command we can see “HEAD → Master, feature1”

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2010.png)

feature1 is a carbon copy of the master, but as said previously, you dont want to be working on production (obvs)

we move to feature1

run the git log cmd again, to heck our work, and we see the order of HEAD to etc has changed

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2011.png)

We create a new file called “feature” add some text, and we go ahead and commit the changes

now when we run the git log command, we can see the latest commit, and it’s separation of the commits to master and feature1

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2012.png)

we make a change to the file, adding “feature commit 2”, and run the git log again we can see the changes we have made to the new branch

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2013.png)

For example our manager comes and says there’s a bug and we need to fix it asap! we switch to master (git checkout). create a dimple file called bugfix, commit it etc (btw, running && works!!)

when we run the git log, we can see on the left hand side that there is a branch (very cool)

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2014.png)

## Merging

Eventually, once a feature has been coded and tested, we want to add it to production! We do this via merging

The way merging works, it takes the original code, and merges the new code to it, so for example, if the prod code was 

```bash
1. print("Hello world")
```

and the feature branch was 

```bash
1. msg = "hello world"
2. print(msg)
```

after the merge the master looks like the feature….. ie msg=”hello world” etc

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2015.png)

(Tutors log output used as you can clearly see the merge)

# Merge Conflicts

Are caused when git doesn’t know what the merge should be?

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2016.png)

So if we take the example of the print(”hello world”) merge, the merge changed the code to 

```bash
msg = "Hello world"
print(msg)
```

but in the above “merge conflict” example, the change is unknown seeing as the code from both would cancel the other out

Easiest way to look at it, branch 1 has a change on line 2, and branch 2 has the same code change in the same location, causing a conflict

In practice, we do this by creating 2x branches with slightly different code

Branch1 = feature/eff  

- The file here will have

```bash
1. msg = "hello world"
2. print(msg)
```

Branch2 = feature2

- The file here will have

```bash
1. msg = "hello planet"
2. print(msg)
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2017.png)

from the feature2 branch, we run 

```bash
#this will cause a conflict
git merge feature/eff
```

as below, we can see git has thrown a “CONFLICT”

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2018.png)

if your running git with an IDE open (in this case VS code) most will show something similar to the below:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2019.png)

# Initialize Remote repos

[lab](https://www.notion.so/Git-Labs-60d87a85b89841a0ba45d2b3d3db360c?pvs=21)

When you setup a remote repo to one of the many online repos (Github, GitLab, Bitbucket - being the most popular), you get a connection string (url - [https://github.com/jadedjelly/git_pull_test.git](https://github.com/jadedjelly/git_pull_test.git))

This is what you will use when pushing code to repos, running the below, adds the url with the alias Origin, meaning every time you need to add a new commit, you can have “origin” instead of having to remember the whole url

```bash
git remote add origin [url]
```

Origin - A name for the remote, you can have multiple

adding and commiting repos to online destinations is exactly the same as doing it local, just add “remote” to the code:

```bash
git remote add origin index.html
```

Useful cmds for remote repos

```bash
#list all remote repos (1st image below: shows output - this shows we are pushing and 
#fetching from the same repo)
git remote -v
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2020.png)

## Pushing to remote repos

Because we need to keep our local and remote repos sync’d we need to push 

```bash
git push origin master
```

The push cmd, expects 2x arguments:

- origin - the url (which is alias’d)
- master - the branch

If we had multiple branches we use

```bash
git push origin feature/emailbutton
```

on the remote repo, it will require a push request (PR), once your create the PR, it gives the option to merge to master. Obvs you don’t want that, so ignore it

## Merging branch into master remotely!

NOTE: This will be a lab as well (~\Desktop\Practice_files)

See [labs](https://www.notion.so/Git-Labs-60d87a85b89841a0ba45d2b3d3db360c?pvs=21)!

Pretty self explanatory

# Rebasing vs Merge vs Squash!

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2021.png)

[https://www.youtube.com/watch?v=0chZFIZLR_0](https://www.youtube.com/watch?v=0chZFIZLR_0) 

Scenario:

You are part of a team, working on a repo, your working on a separate feature and the rest of the team are working on master. Because the rest of the team are constantly making commits, your version is out of sync, there are a few options:

- Rebasing
- Merging
- Squashing

**Git merge** pulls in the latest changes to the feature branch, creating a new merge commit in the process

**git rebase** changes the latest commit (on branch) to the main

see below diagrams

From:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2022.png)

to:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2023.png)

Keeps commit history clean!

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2024.png)

Left messy commit history

right neat / clean history

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2025.png)

git squash This moves all commits into the latest commit, see below:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2026.png)

the only downside is the history, on a squash, all commit history is in a single file, where as a rebase and merge each individual commit is recorded

# Interactive Rebase

Say 

your working on a branch, and you have made a heap of changes that should have really been done on the 1st commit. You can use interactive rebase to condense this from 4 commits into 1.

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2027.png)

running:

git rebase -i HEAD~4 

will execute as below:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2028.png)

this is where git helps you by showing the above in the local env terminal editor

so if you wanted to reword a commit, you can either use r or reword, etc

# Cherry pick commits

Say your working on something with a team, and you know another dev made a commit on a branch, you can “cherry pick” that commit and send it to your local, by running:

git cherry-pick [hash of that commit]

# Resetting and Reverting

Say you make a mistake in a commit, to undo the change there are two options:

- revert:
    - git revert [hash of commit] (the one thats the mistake)
- Reset - 2 options:
    - git reset —soft HEAD~1
        - Deletes the commit messages & keeps the changes
    - git reset —hard HEAD~1
        - Deletes the messages & the commits, no changes are kept
            - Similar to revert

# Stashing

This is like temporarily moving staged files into a another folder. Example, say your working on a file, and a high priority issues comes in, you stash the file and work on the high priority, then when your done you can pull the file from the stashed location and continue working.

To stash a file, the file must be in the staged area (run git add [file name])

To stash a staged file run,

```bash
git stash
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2029.png)

To see a list of stashed file run,

```bash
git stash list
```

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2030.png)

if you want to have a look at a specific file from the stash run,

```bash
git stash show stash@{stash#}
```

Use git stash list to view the stash number for a file

To move a specific file from stash to the staged area, run

```bash
#move speciifc file
git stash pop stash@{stash#}

#to move the last stashed file run:
git stash pop
```

# Reflog

Used along with “reset”, reflog shows all changes on a repo, as below:

![Untitled](Git%20for%20Beginners%20595eab72c5074274abf1c8d2a6e293cb/Untitled%2031.png)