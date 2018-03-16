# Git Collaboration

## Objectives

1. Make a new branch for your repository with `git branch`.
2. Checkout a branch with `git checkout`.
3. Create and checkout a new branch with `git checkout -b`.
4. Create commits within a branch.
5. Merge branches with `git merge`.
6. Update branches from remotes with `git fetch`.
7. Merge updated remote branches with `git merge`.
8. Update and merge remote branches with `git pull`.

## Overview

An important part of collaboring ih git is to keep discrete, individual lines of work separate from each other. "Commit early, commit often", or some variation of this phrase, is commonly used by developers. 
**Why is that?**--The idea behind small, frequent commits is to allow developers to track their work in easy-to-define chunks. For instance, if you make various unlreated changes and commit all of the code at once (eg: you add several new files, change some stylesheets, install a couple new packages/gems, update some config files, and delete some views), you, let alone other developers, may not have any idea how much those changes impact the overall application, and would make tracking down and implementing fixes very difficult, in the occasion that something you did broke the app. With that being said, consider the following scenario:

> You begin working on a big feature. You make a few "Work in Progress" commits that don't get the feature fully-functional. Your git log might look like:
> ```
> 512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
> 62d840 Almost done with new-feature (aviflombaum, 1 day ago)
> fbee832 Started new-feature (aviflombaum, 2 days ago)
> ```
> This feature has been in progress for 2 days. Between yesterday and today, lots of progress was made, but it's still not working yet. In its current state, if we had to push the changes to the git repository, the latest version of our code would deploy to production and go live and our users would see a half-finished, currently broken new-feature--**That's no good!**
>
This isn't that big of a deal, _right?_

>One option would be to wait until the new feature is complete, push the changes to the repository, and have the changes go live to our users. 

**This is where things get tricky...**
> We notice a major bug that is currently breaking the application for all users. It's an easily fixable bug--one simple change to the code would make everything work again. Unfortunately, if you make and commit that fix, you wouldn't be able to deploy change. Even though that commit may fix the bug, the feature that you were previously working on would still be half-finished, and the users would see a broken new feature.

Now your commit messages me look like the below: 
```
r4212d1 Fix to application breaking bug (aviflombaum, just now)
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```
It wouldn't be wise to push all these commits. What if we were able to isolate our work the new feature into its own copy of our code, so that until it's done, we could deploy the commit that fixes the application? We can do exactly this using a feature in git called **branches**.

## Making a branch with `git branch`

First, let's walk through the process of making a repository as a sandbox to experiment with the collaborative features of git. You don't have to follow along--you'll be able to understand the concepts from the reading, but for practice, you can copy and paste the following commands into your computer's terminal window.

From our home directory (visible when you first open up the terminal), we'll make a new directory for our mission-critical-application.

1.
```
~ $ mkdir mission-critical-application
```
This will make a new directory in your current folder
2.
```
~ $ cd mission-critical-application
```
This command will change directory, placing you inside of the newly created folder `mission-critical-application`
3.
```
mission-critical-application $ git init
```
This initializes a git repository so that you can push and pull changes.
4.
```
mission-critical-application $ touch application.rb
```
**touch** creates a new file. The new file is `application.rb` where we create our application.
5.
```
mission-critical-application $ git add application.rb
```
`git add` will **stage** your working changes so that they can will be included when you run the **commit** command.
6.
```
mission-critical-application $ git commit -m "First working version of application.rb"
```
This creates a commit for your newly created `application.rb` file, with a clear and concise commit message that logs this in the history.

8. Your application is deployed to production and people start using it (_not reflected in the command lines above, but let's assume you did a great job!_).

Now that our application is online and customers are rolling in, we notice a bug and quickly add a fix in the form of a file, for the sake of the example, is called `first-bug-fix.rb`.

We create, stage, and commit the file using the commands below:
```
mission-critical-application $ touch first-bug-fix.rb
mission-critical-application $ git add first-bug-fix.rb
mission-critical-application $ git commit -m "First bug fix"
```

Right now, our git log could be visualized as a timeline composed of two commits:

![First Two Commits](https://dl.dropboxusercontent.com/s/ikorf1qvvp4tay0/2015-11-02%20at%2011.15%20AM.png)

### About `master` branch.

Notice that these commits are occurring in a linear sequence of events, almost like a timeline? We call this timeline a **branch** (sort of like a tree)! Whenever you are working on commits in git, you are adding them on a timeline of code called a branch. The branch you are on by default at the start of any repository, your main timeline, the main branch is called master.

![Master Branch](https://dl.dropboxusercontent.com/s/v75as2cf6xr8n8a/2015-11-02%20at%2011.17%20AM.png)

If you're not sure which branch you're currently working on, the command `git status` will tell you.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
```

The git branch called `master` is our default branch. One of the responsible ways to use git, is to make sure that the `master` branch is always "clean" with working code. So, if we ever need to add a bug fix, we can do so and deploy a new version of the application immediately. If we keep broken or incomplete code out of master, we can always deploy master safely without negatively impacting the rest of the application.

### Starting a new feature with `git branch new-feature`

To keep master clean, when we want to start a new feature, we should do it in an isolated feature branch. Our timeline will look as follows:

![Feature Branch](https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

After commit 2, we will branch out of master and create a new timeline for commits and events specifically related to the new feature. The master timeline remains unchanged and clean. Now that we've covered the idea of the new-feature branch, let's actually make it.

To make a new branch simply type: `git branch <branch name>`. In the case of a branch relating to a new feature, we'd name the branch `new-feature` like so:

```
mission-critical-application $ git branch new-feature
```

To see a list of our branches we can type: `git branch -a`

```
mission-critical-application $ git branch -a
* master
  new-feature
```

The `*` in front of the branch `master` indicates that `master` is currently our working branch and git tells us that we also have a branch called `new-feature`. If we made a commit right now, that commit would still be applied to our `master` branch.

### Switching branches with `git checkout`

We need to checkout or move into our `new-feature` timeline or branch so that git knows that all commits made apply to only that unit of work, timeline, or branch. We can move between branches with `git checkout <branch name>`.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
mission-critical-application $ git status
On branch new-feature
nothing to commit, working directory clean
```

We started on `master` and then checked out our `new-feature` branch with `git checkout new-feature`, thereby moving into that timeline.

Let's make a commit in this `new-feature` and get the feature started by making a new file, `new-feature-file` to represent the code for the new feature.

```
mission-critical-application $ touch new-feature-file
mission-critical-application $ git add new-feature-file
mission-critical-application $ git commit -m "Started new feature"
[new-feature 332a618] Started new feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature-file
```

You can see the commit we made was made in the context of the `new-feature` branch.

Right as we got started on that feature though, we get another bug report and have to move back into master to fix the bug and then deploy master. How do we move from `new-feature` branch back to `master`? What will our code look like when we move back to `master`, will we see the remnants of the `new-feature` branch and code represented by the `new-feature-file`?

**Protip: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create the branch `new-branch-name` and move into it by checking it out.**

#### Moving back to `master` with `git checkout master`

You can always move between branches with `git checkout`. Since we are currently on `new-feature`, we can move back to master with `git checkout master`.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

And we could move back to `new-feature` with `git checkout new-feature`

```
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

And back again with:

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

![Switching between branches](https://dl.dropboxusercontent.com/s/qzajqsd9f6njauc/2015-11-02%20at%2012.12%20PM.png)

From master, one thing you'll notice is that the code you wrote on `new-feature`, namely the file, `new-feature-file`, is not present in the current directory.

```
mission-critical-application $ ls
application.rb first-bug-fix.rb
```

The master branch only has the code from the most recent commit relative to the master timeline or branch. The code from our `new-feature` is tucked away in that branch, waiting patiently in isolation from the rest of our code in `master` for us to finish the feature.

Once you're on master you are free to make a commit to fix the bug, which we'll represent with a new file, `second-bug-fix.rb`.

```
mission-critical-application $ touch second-bug-fix.rb
mission-critical-application $ git add second-bug-fix.rb
mission-critical-application $ git commit -m "Second bug fix"
```

Let's look at our timeline now.

![Commit on Master](https://dl.dropboxusercontent.com/s/9ipgkog7yv8hrok/2015-11-02%20at%2012.18%20PM.png)

We were able to update the timeline in master with the fix to the bug without touching any of the code in new-feature. `new-feature` branch and timeline remains 1 commit behind master, because the second bug fix commit occured in master and `new-feature` branch was created only with the commits at the moment when the branch was created. You could describe `master` as being 1 commit ahead of the `new-feature` branch.

Let's go back into `new-feature` and complete the feature and commit it and then look at the timeline. Remember how to move from `master` back to `new-feature`?

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

Let's rename `new-feature-file` to `new-feature` to signify the code we wrote to complete the feature and commit this change. We can rename a file with `mv <original filename> <new filename>` BASH command.

```
mission-critical-application $ mv new-feature-file new-feature
mission-critical-application $ git add new-feature
mission-critical-application $ git commit -m "Finished feature"
[new-feature bfe50fc] Finished feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Let's look at our timeline now.

![Completed Feature Branch](https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

The final step of our `new-feature` work sprint is to figure out how to merge that timeline into the master timeline.

## Merging branches with `git merge`

Our goal is to bring the timeline of commits that occurred on the `new-feature` branch into the `master` so that at the end of the operation, our `master` timeline looks like:

![Merged Timeline](https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

By merging the timelines, `master` will have all of the commits from the `new-feature` branch as though those events occured on the `master` timeline.

When we merge a branch with `git merge`, it's important to be currently working on your target branch, the branch you want to move into. The first step for our `new-feature` merge is to checkout `master` because that is where we want the commits to end up.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

Once on your target branch, type: `git merge <branch name>` where `<branch name>` is the branch we want to merge. In this case, `git merge new-feature` will do the trick.

```
mission-critical-application $ git merge new-feature
Updating e5830af..bfe50fc
Fast-forward
 new-feature      | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Now the branches have been merged and if you `ls`, you'll see the `new-feature` file from the `new-feature` branch in your current working directory that is checked out to master.

## Working with remote branches with `git fetch` and `git pull`

Your local branches can attach to remote branches that live on the internet, generally on GitHub, that your team members might contribute to and you can download locally.

Whenever you want to update your local copy with all the branches that might have been added to the GitHub remote, you can type `git fetch`.

```
mission-critical-application $ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 3), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), done.
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

From within `master` (though technically what branch I was in when I typed `git fetch` does not matter), I executed `git fetch`. The last 3 lines of output are really important, let's take a closer look:

```
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The first line, `From github.com:aviflombaum/mission-critical-application` is informing us which remote our `git fetch` updated from, namely, the remote repository located at: https://github.com/aviflombaum/mission-critical-application

When we `fetch` with git, we are asking to copy all changes on the remote to our local git repository, but not actually integrate any. The next line, `bfe50fc..0ae1da2  master     -> origin/master` is telling us that a new commit was found in `origin/master`. `origin/master` means the GitHub version of `master`. Even though git fetched a new commit from `origin/master`, it did not merge it into the local master.

![Fetch without integration](https://dl.dropboxusercontent.com/s/iy2jovft8ykrxbd/2015-11-02%20at%202.08%20PM.png)

Our remote copy on GitHub has a file, `remote-bug-fix`, presumably some code that another developer pushed up to our remote version of the `master` branch to fix a bug. Even after we fetched, our local copy still doesn't appear to have that file.

After you fetch, you have access to the remote code but you still have to merge it. How do you merge a change fetched from `origin/master` into your current master? From within your local master branch, type: `git merge origin/master`, referring to the branch's full path, `remote/branch`, or `origin/master`.

```
mission-critical-application $ git merge origin/master
Updating bfe50fc..0ae1da2
Fast-forward
 remote-bug-fix | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 remote-bug-fix
mission-critical-application $ ls
 application.rb		new-feature-file    new-feature		
 remote-bug-fix
```

The commits fetched via `git fetch` are now merged from the `origin/master` branch into our local `master` branch. And now `ls` reveals that the file present on the remote, `remote-bug-fix` is integrated into our local copy of `master` as well.

When we fetched, git also outputted: `* [new branch]      remote-feature-branch -> origin/remote-feature-branch`. Similarly, git fetched a new branch and if we want to check it out or merge it we can using `git checkout` or `git merge`. Let's checkout what code is on `remote-feature-branch`, a branch another developer made for another feature and pushed up to GitHub so they can share it with us.

```
mission-critical-application $ git checkout remote-feature-branch
Branch remote-feature-branch set up to track remote branch remote-feature-branch from origin.
Switched to a new branch 'remote-feature-branch'
```

When we checkout a remote branch fetched, git will create a local branch to track that remote and switch to that branch. We can now do work, push it back up to GitHub, and another developer can fetch those changes down.

`git fetch` is a pretty low-level git command we don't use that much because it always requires two steps, first `git fetch` and then `git merge` to actually integrate those changes into your working branch. Generally, if you are in `master` you want to immediately `fetch` and `merge` any changes to the remote master.

### Combining `git fetch` with `git merge` by using `git pull`

If you want to both fetch and merge, which is what you want to do 99% of the time, just type `git pull`. `git pull` is literally the combination of both `git fetch` and `git merge`.

When you `git pull` the following things will occur:

1. You will `git fetch` all remote changes, including those on the current branch, existing branches, and new branches.
2. Any changes that are on a remote branch which is being tracked by your local branch, that is to say, if you are on `master` and there is a change to `origin/master`, those changes will be automatically merged.

## Conclusion

Git is complex, and collaborating with people in this matter is just hard - there's no easy way to allow 100s of people to all work on the same code base. These workflows are just being introduced to you.  You'll have lots of time to practice them and memorize what each command does. Don't try to learn it all at once; instead just start to get an understanding of what's what.

![XKCD Git](http://imgs.xkcd.com/comics/git.png)

<a href='https://learn.co/lessons/git-collaboration-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>

<p class='util--hide'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>
