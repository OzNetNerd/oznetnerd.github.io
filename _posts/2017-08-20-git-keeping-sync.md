---
title: 'Git: Keeping in sync'
sub_heading: "Sharing is caring"
redirect_from: /git-keeping-sync/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
---
In the [**Getting start with git**](/getting-started-with-git/) post we covered a number of things, one of which was using `push` to send our commits to a remote git repo. This is works fine when **both** of these conditions are met:

1.  You're the only person working on the project
2.  You're doing all of your development from the same machine

If one of these points is not true, you'll soon find the `push` command fails to work. The reason for this is because you must first retrieve all of the commits from the remote branch before you can merge your own. In other words, you must first be in sync before you can make modifications.

Therefore if someone does a `push` before you, and/or you do a `push` from a different machine, the machine you're currently using will be out of sync with the remote branch. As a result you will be unable to do a `push` until you first re-sync with the remote branch.

**Note:** The reason why this issue is not encountered when you meet the two criterion listed above is because your machine will always be in sync with the remote branch given that it's the only one doing any commits.

Let's now run through an example to see this in action.

## Step 1: Update the remote branch

The first thing we'll need to do is update the remote branch. Rather than have someone else do a `push` or me use another machine to do the `push`, I'm going to create a new file through the GitLab web GUI. Doing so automatically makes a commit on the remote branch and therefore makes my machine's copy out of sync.

[![](/assets/2017/08/commit-1.png)](/assets/2017/08/commit-1.png)

After clicking **Commit change** my local copy of `origin/master` is now out of date. However, my machine doesn't know that yet:

```
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

## Step 2: Make modifications locally

Next we need to make a modification locally, whether it be the creation, modification or deletion of a file that will be/is currently tracked by the git. Let's go ahead and create a new file, stage and commit it:

```
will@ubuntu:/tmp/git-tutorial$ echo 'this is a new file' > new_file.txt
will@ubuntu:/tmp/git-tutorial$ git add .
will@ubuntu:/tmp/git-tutorial$ git commit -m 'adding new_file.txt'
[master 11f324c] adding new_file.txt
 1 file changed, 1 insertion(+)
 create mode 100644 new_file.txt
 ```
 
So far, so good! Now let's `push` our commit the same way we did [**last time**](/getting-started-with-git/):

```
will@ubuntu:/tmp/git-tutorial$ git push
To git@gitlab.com:OzNetNerd/git-tutorial.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@gitlab.com:OzNetNerd/git-tutorial.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

What happened?! Oh yes, that's right, `Updates were rejected because the remote contains work that you do not have locally`.

## Getting back in sync

At this point the `git status` output can be a little confusing because it tells us that it knows about our local commit, but says nothing about the commit on the remote branch:

```
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
nothing to commit, working directory clean
```

The reason for this is because git doesn't update unless you specifically tell it to do so. Let's go ahead and do that now, but instead of using the `git pull` command mentioned in the error output, let's use `git fetch` instead:

```
will@ubuntu:/tmp/git-tutorial$ git fetch
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From gitlab.com:OzNetNerd/git-tutorial
   dbc082d..3ae1bd9  master     -> origin/master
```

Excellent, our `origin/master`  has now **fetched** the latest information and is in sync with the remote git server.

If we issue the `git status` command again we can see that git now knows about both our commit, as well as the commit which was made through the GUI:

```
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch and 'origin/master' have diverged,
and have 1 and 1 different commit each, respectively.
  (use "git pull" to merge the remote branch into yours)
nothing to commit, working directory clean
```

However, if we try to `push` our commit again we'll get a new error:

```
will@ubuntu:/tmp/git-tutorial$ git push
To git@gitlab.com:OzNetNerd/git-tutorial.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'git@gitlab.com:OzNetNerd/git-tutorial.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

In this output git is telling us that our local `master` branch is out of sync with the remote `origin/master` branch and therefore we cannot `push` until they're in sync. In order to sync them, git tells us to use the `git pull` command.

However, because we've already done a `git fetch`, we can instead use the `git merge` command to merge the `origin/master` branch's changes into our `master` branch, as shown below.

**Note:** The `git pull` command does a `git fetch` and `git merge`. Therefore seeing as though we had already done a `git fetch`, both the `git pull` and `git merge` commands would have resulted in the same outcome.

```
will@ubuntu:/tmp/git-tutorial$ git merge -m 'merger'
Merge made by the 'recursive' strategy.
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
 ```

Excellent, our merge was successful! Let's now check the `git status` output:

```
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)
nothing to commit, working directory clean
```

OK so now we're two commits ahead of `origin/master`. This is makes sense because the first commit would be the change we made on `master` (remember the `echo 'this is a new file' > new_file.txt` command?), and the second change is one one we did in Step 1 being merged in.

Let's now push our local `master` branch to the remote `origin/master` branch:

```
will@ubuntu:/tmp/git-tutorial$ git push
Counting objects: 5, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 565 bytes | 0 bytes/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To git@gitlab.com:OzNetNerd/git-tutorial.git
   e74953c..35558fb  master -> master
```

Finally, let's do a git status to ensure that our local and remote branches are in sync:

```
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

## Final Notes

I want to finish off this post with some final notes which might not have been abundantly clear to newcomers:

*   The `git fetch`, `git merge` and `git pull` are all done locally on our machine. To put it another way, the merging of the `master` and `origin/master` branches are done on our machine. Only when we do a `git push` are our changes published to the remote repo where others can see them. Therefore there's no need to panic if you make a mistake with your fetching, merging and/or pulling, so long as you don't then do a push.
*   In this post we merged one remote commit into our one local commit. This resulted in us having to push two commits to the remote repo. What you might not know though is that even if there were three remote commits and one local commit, we would still only push two commits to the remote repo - the first one being our local commit, and the second being all of the other commits wrapped up into a single commit. Let's run through a quick example now...

Let's make three commits on our remote repo (again using the GitLab GUI). Let's call them `file7.txt`, `file8.txt` and `file9.txt`. Let's also create a file locally on our machine called `file10.txt`.

Because we used the GitLab GUI to create the three remote files, they were each committed separately. This therefore resulted in three commits being made. Let's now commit our `file10.txt` locally:

```
will@ubuntu:/tmp/git-tutorial$ git add .
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD ..." to unstage)

	new file:   file10.txt

will@ubuntu:/tmp/git-tutorial$ git commit -m 'Added new file - file10.txt (local)'
[master 38be65d] Added new file - file10.txt (local)
 1 file changed, 1 insertion(+)
 create mode 100644 file10.txt
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
``` 

OK great, we can see that we're one commit ahead of `origin/master`. Let's now `fetch` the latest commits so that we can update our copy of `origin/master` and see the real difference in commits:

```
will@ubuntu:/tmp/git-tutorial$ git fetch
remote: Counting objects: 8, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 8 (delta 3), reused 1 (delta 0)
Unpacking objects: 100% (8/8), done.
From gitlab.com:OzNetNerd/git-tutorial
   f0162f0..afe707e  master     -> origin/master
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch and 'origin/master' have diverged,
and have 1 and 3 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
nothing to commit, working directory clean
```

Now we've got the full story. We've made one change locally, but there have been three changes made remotely. To clean this situation up, we can use a `git pull` or `git merge` as discussed previously. Let's do a `git pull` this time around:

```
will@ubuntu:/tmp/git-tutorial$ git pull
Merge made by the 'recursive' strategy.
 file7.txt | 1 +
 file8.txt | 0
 file9.txt | 0
 3 files changed, 1 insertion(+)
 create mode 100644 file7.txt
 create mode 100644 file8.txt
 create mode 100644 file9.txt
will@ubuntu:/tmp/git-tutorial$ 
will@ubuntu:/tmp/git-tutorial$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)
nothing to commit, working directory clean
```

And there we have it. Although there were a total of four commits originally, we end up with only two. This is known as a **merge commit**. For information on Recursive merges as well as Rebasing, please see the [**Git: Merging & Rebasing basics**](/git-merging-rebasing-basics/) post.