---
title: 'Git: Merging & Rebasing basics'
sub_heading: "Take you Git skills to the next level"
redirect_from: /git-merging-rebasing-basics/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
---
In the [**Git: Keeping in sync**](/git-keeping-sync/) post we learned how to **merge** the `orgin/master` commits into our local `master` branch. Then in **[Git: Effective branching using workflows](/git-effective-branching-workflows/) **we learned about how to use branches effectively. What we haven't yet touched on yet though is **rebasing** and its affect on merging.

## Commit log

Before we get started on merging and rebasing, let's first see how we can view our `git log` as we will need to do it throughout this post:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (master)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (HEAD -> master, origin/master, develop)
a0ec1b6 - (4 days ago) removed file1.txt - Will Robinson (origin/test_branch)
740a573 - (4 days ago) Add new file - Will
```

In a nutshell, the above command shows us the last three commits which were made in this repo. If we want to get a little fancier, we can have git draw a graph for us:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (master)
$ git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
*   0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (HEAD -> master, origin/master, develop)
|\
| * a0ec1b6 - (4 days ago) removed file1.txt - Will Robinson (origin/test_branch)
| * 740a573 - (4 days ago) Add new file - Will
```

_Note: The above commands were taken from [**this StackOverflow thread**](https://stackoverflow.com/questions/1057564/pretty-git-branch-graphs). As per the screenshots on StackOverflow, the output is colour coded which definitely helps!_

You're probably wondering what the `*`, `|` and `\` characters represent. They're used to show us the branches and commits which have been created in this repo. By sticking to a [**git workflow**](/git-effective-branching-workflows/), your log will be tidy and easy to follow. If you don't, it's highly likely your log is already or soon will be a complete mess! But I digress...

## Creating a branch

Let's start by creating a new branch off `master` called `develop`:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (master)
$ git checkout -b develop
Switched to a new branch 'develop'
```

Now let's make it track remotely:

```
$ git push -u origin develop
Total 0 (delta 0), reused 0 (delta 0)
remote:
remote: To create a merge request for develop, visit:
remote:   https://gitlab.com/OzNetNerd/git-tutorial/merge_requests/new?merge_request%5Bsource_branch%5D=develop
remote:
To https://gitlab.com/OzNetNerd/git-tutorial.git
 * [new branch]      develop -> develop
Branch develop set up to track remote branch develop from origin.
```

Finally, let's confirm that it is indeed being tracked remotely:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git branch -avv
* develop                    0a37254 [origin/develop] Merge remote-tracking branch 'origin/test_branch'
  master                     0a37254 [origin/master] Merge remote-tracking branch 'origin/test_branch'
  remotes/origin/develop     0a37254 Merge remote-tracking branch 'origin/test_branch'
  remotes/origin/master      0a37254 Merge remote-tracking branch 'origin/test_branch'
```

Great, the above tells us that `develop` is tracking `origin/develop`. We're good to go!

## Merging

Let's now delve into the whole reason (or at least, one of the reasons) for this post - Merging.

As its name suggests, performing a `merge` results in two branches being merged. While it might sound easy enough, that isn't the end of the story. The state of the two branches (the `origin` and `local` branches) determines the way in which they will be merged. Let's take a look at how to initiate a Fast Forward merge, as well as a Recursive merge.

### Fast Forward

Fast Forward merges are very straightforward (pun intended!). They can be used when there is no divergence between the remotely tracked `origin` branch and our `local` branch.

It's known as a Fast Forward merge because git is able to simply move the branch's pointer forward to the tip of the merged branch. It's as simple as that. This is in contrast to other merging methods whereby complex merging strategies are required.

The benefit of a Fast Forward merge is that it keeps your `git log` graph linear/clean. Let's now look at an example.

Below is what our newly created `develop` branch looks like:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (HEAD -> develop, origin/master, origin/develop, master)
a0ec1b6 - (4 days ago) removed file1.txt - Will Robinson (origin/test_branch)
740a573 - (4 days ago) Add new file - Will
```

At this point in time, `develop` and `origin/develop` are in sync. However, let's say we go to lunch and when we come back, John has made a few commits and performed a `push`  to the `origin/develop` branch:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 1), reused 0 (delta 0)
Unpacking objects: 100% (4/4), done.
From https://gitlab.com/OzNetNerd/git-tutorial
   0a37254..a655b2f  develop    -> origin/develop

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
a655b2f - (57 seconds ago) Add new file: script2.py - John (origin/develop)
c64c510 - (81 seconds ago) Add new file: script1.py - John
0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (HEAD -> develop, origin/master, master)
```

In the above output we can see that the `fetch` command was used to sync our machine's copy of `origin/develop` with the remote server's version. Because we saw new commits come through, we then looked at the `git log` and saw there were in fact two new commits.

So now our `origin/develop` has been updated, but our local `develop` branch still hasn't. Because we haven't made any commits to our local branch, we can see that the path between `develop` and `origin/develop` is straight:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3 --graph
* a655b2f - (4 minutes ago) Add new file: script2.py - John (origin/develop)
* c64c510 - (4 minutes ago) Add new file: script1.py - John
*   0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (HEAD -> develop, origin/master, master)
```

As a result of this, if we do a `git merge` or a `git pull` (which, as mentioned in the [**Git: Keeping in sync**](/git-keeping-sync/) post, does a `git fetch` and `git merge` in a single command), it will result in a Fast Forward merge:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git merge
Updating 0a37254..a655b2f
Fast-forward
 script1.py | 0
 script2.py | 0
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 script1.py
 create mode 100644 script2.py
```

Let's now take a look at what our ``git log`` looks like:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
a655b2f - (12 minutes ago) Add new file: script2.py - John (HEAD -> develop, origin/develop)
c64c510 - (12 minutes ago) Add new file: script1.py - John
0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (origin/master, master)

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3 --graph
* a655b2f - (12 minutes ago) Add new file: script2.py - John (HEAD -> develop, origin/develop)
* c64c510 - (12 minutes ago) Add new file: script1.py - John
*   0a37254 - (4 days ago) Merge remote-tracking branch 'origin/test_branch' - Will Robinson (origin/master, master)
```

Excellent, `develop` and `origin/develop` are in sync, and we've got a tidy graph log.

### Recursive merge

As mentioned previously in this post, a Fast Forward merge can only take place when there's no divergence between the `origin` and `local` branches. Let's see what happens when they do diverge.

Using the same example as before, let's say John made a few commits and used `push` to send them to the `origin/develop` branch. We then do a `git fetch` and check `git status` as well as `git log` to find out how many commits were made:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git status
On branch develop
Your branch is behind 'origin/develop' by 2 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)

nothing to commit, working tree clean

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 2), reused 0 (delta 0)
Unpacking objects: 100% (4/4), done.
From https://gitlab.com/OzNetNerd/git-tutorial
   a655b2f..b5c11ab  develop    -> origin/develop

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
b5c11ab - (2 minutes ago) Add new file: script4.py - John (origin/develop)
5ff754d - (2 minutes ago) Add new file: script3.py - John
a655b2f - (29 minutes ago) Add new file: script2.py - John (HEAD -> develop)

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3 --graph
* b5c11ab - (2 minutes ago) Add new file: script4.py - John (origin/develop)
* 5ff754d - (3 minutes ago) Add new file: script3.py - John
* a655b2f - (29 minutes ago) Add new file: script2.py - John (HEAD -> develop)
```

git tells us that we're two commits behind `origin/develop`. Instead of doing a `git merge` like we did last time, this time we make a couple of commits of our own:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ touch new_script1.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git add .

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git commit -m 'Added file: new_script1.py'
[develop e21cdc2] Added file: new_script1.py
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new_script1.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ touch new_script2.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git add .

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git commit -m 'Added file: new_script2.py'
[develop 4ea7d92] Added file: new_script2.py
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new_script2.py
```

Let's now take a look at the `git status` and `git log` outputs:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git status
On branch develop
Your branch and 'origin/develop' have diverged,
and have 2 and 2 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -5 --graph
* 4ea7d92 - (2 minutes ago) Added file: new_script2.py - Will (HEAD -> develop)
* e21cdc2 - (2 minutes ago) Added file: new_script1.py - Will
| * b5c11ab - (8 minutes ago) Add new file: script4.py - John (origin/develop)
| * 5ff754d - (8 minutes ago) Add new file: script3.py - John
|/
* a655b2f - (35 minutes ago) Add new file: script2.py - John
```

There are two important pieces of information in this output:

1.  git tells us that our `develop` branch has diverged from `origin/develop`.
2.  Our `git log` graph isn't linear like it was last time. We can see that our `develop` branch diverged from `origin/develop` at commit `a655b2f` .

The good news is that we can still use our trusty `git merge` (or `git pull`) command to bring our `local` back into sync with the `origin` branch. However, due to the divergence, a Recursive merge will be used instead of a Fast Forward merge:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git merge

Merge made by the 'recursive' strategy.
 script3.py | 0
 script4.py | 0
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 script3.py
 create mode 100644 script4.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git push
Counting objects: 6, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 671 bytes | 671.00 KiB/s, done.
Total 6 (delta 3), reused 0 (delta 0)
remote:
remote: To create a merge request for develop, visit:
remote:   https://gitlab.com/OzNetNerd/git-tutorial/merge_requests/new?merge_request%5Bsource_branch%5D=develop
remote:
To https://gitlab.com/OzNetNerd/git-tutorial.git
   b5c11ab..7ae9fcf  develop -> develop

Let's now take a look at our `git log` outputs:

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -5
7ae9fcf - (5 minutes ago) Merge remote-tracking branch 'refs/remotes/origin/develop' into develop - Will (HEAD -> develop, origin/develop)
4ea7d92 - (18 minutes ago) Added file: new_script2.py - Will
e21cdc2 - (18 minutes ago) Added file: new_script1.py - Will
b5c11ab - (24 minutes ago) Add new file: script4.py - John
5ff754d - (25 minutes ago) Add new file: script3.py - John

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -6 --graph
*   7ae9fcf - (5 minutes ago) Merge remote-tracking branch 'refs/remotes/origin/develop' into develop - Will (HEAD -> develop, origin/develop)
|\
| * b5c11ab - (24 minutes ago) Add new file: script4.py - John
| * 5ff754d - (25 minutes ago) Add new file: script3.py - John
* | 4ea7d92 - (18 minutes ago) Added file: new_script2.py - Will
* | e21cdc2 - (18 minutes ago) Added file: new_script1.py - Will
|/
* a655b2f - (52 minutes ago) Add new file: script2.py - John
```

As we can see, `7ae9fcf` is a merge commit and has resulted in our local `develop` branch and the `origin/develop` branch being brought back into sync.

## Rebasing

While the two preceding merging sections resulted in the same outcome, they took two different paths to get there. It's important to note that some prefer to avoid the Recursive merging strategy because it pollutes the log history and goes against their [**chosen git workflow**](/git-effective-branching-workflows/).

You might be thinking though, "are you telling me I have to do a `git fetch` and `git merge`  or a `git pull` every single time I'm about to do a `git push` just so I can avoid polluting my `git log`?!". The answer to that question, thankfully, is no. This is where rebasing comes into play.

What rebasing does is it puts your `local` commits at the end of the `remote` (origin) commits, therefore creating a linear path resulting in the allowance of a Fast Forward merge. Wow, that was a bit of a mouthful, but rest assured it's much easier than it sounds.

As with the previous example, let's say John has made a couple of commits and used `push` to send them to `origin/develop`. We then make a change and commit it. We're then ready to `push` our changes to `origin/develop` but find that there's a divergence:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ touch new_script3.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git add .

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git commit -m 'Add new file: new_script3.py'
[develop eed15d9] Add new file: new_script3.py
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new_script3.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 2), reused 0 (delta 0)
Unpacking objects: 100% (4/4), done.
From https://gitlab.com/OzNetNerd/git-tutorial
   7ae9fcf..f693496  develop    -> origin/develop

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git status
On branch develop
Your branch and 'origin/develop' have diverged,
and have 1 and 2 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean
```

As we saw in the **Recursive merge** section of this post, if we do a `git merge` now we'll end up with an untidy `git log` graph. Let's then do a rebase instead:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git pull --rebase
First, rewinding head to replay your work on top of it...
Applying: Add new file: new_script3.py

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git push
Counting objects: 2, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 251 bytes | 251.00 KiB/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote:
remote: To create a merge request for develop, visit:
remote:   https://gitlab.com/OzNetNerd/git-tutorial/merge_requests/new?merge_request%5Bsource_branch%5D=develop
remote:
To https://gitlab.com/OzNetNerd/git-tutorial.git
   f693496..f9ac684  develop -> develop
```

Let's now take a look at our `git log` outputs:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -9
f9ac684 - (23 minutes ago) Add new file: new_script3.py - Will (HEAD -> develop, origin/develop)
f693496 - (27 minutes ago) Delete script4.py - John
c4fbeba - (27 minutes ago) Delete script3.py - John
7ae9fcf - (49 minutes ago) Merge remote-tracking branch 'refs/remotes/origin/develop' into develop - Will
4ea7d92 - (61 minutes ago) Added file: new_script2.py - Will
e21cdc2 - (62 minutes ago) Added file: new_script1.py - Will
b5c11ab - (68 minutes ago) Add new file: script4.py - John
5ff754d - (68 minutes ago) Add new file: script3.py - John
a655b2f - (2 hours ago) Add new file: script2.py - John

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -9 --graph
* f9ac684 - (24 minutes ago) Add new file: new_script3.py - Will (HEAD -> develop, origin/develop)
* f693496 - (27 minutes ago) Delete script4.py - John
* c4fbeba - (27 minutes ago) Delete script3.py - John
*   7ae9fcf - (49 minutes ago) Merge remote-tracking branch 'refs/remotes/origin/develop' into develop - Will
|\
| * b5c11ab - (68 minutes ago) Add new file: script4.py - John
| * 5ff754d - (68 minutes ago) Add new file: script3.py - John
* | 4ea7d92 - (61 minutes ago) Added file: new_script2.py - Will
* | e21cdc2 - (62 minutes ago) Added file: new_script1.py - Will
|/
* a655b2f - (2 hours ago) Add new file: script2.py - John
```

Hooray! We've managed to merge the branches even though they were diverged, while still maintaining a linear (clean) history.

### How does rebasing work?

Rebasing gets its name from the fact that it changes the commit which your subsequent commits are based off - in other words, your commits are rebased onto another commit.

Let's take a look at another example. Let's say the following commits were made in this order:

|||||
|--- |--- |--- |--- |
|Commit:|Author:|Filename:|Branch:|
|1|John|menu.py|origin/delveop|
|2|Will|module.py|develop|
|3|John|food.py|origin/develop|


The `git log` outputs confirm that the above is correct:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -4
9ec5884 - (70 seconds ago) Add new file: food.py - John (origin/develop)
108334e - (2 minutes ago) Add new file: module.py - Will (HEAD -> develop)
3feff89 - (2 minutes ago) Add new file: menu.py - John
f9ac684 - (40 minutes ago) Add new file: new_script3.py - Will

$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -4 --graph
* 9ec5884 - (54 seconds ago) Add new file: food.py - John (origin/develop)
* 3feff89 - (2 minutes ago) Add new file: menu.py - John
| * 108334e - (89 seconds ago) Add new file: module.py - Will (HEAD -> develop)
|/
f9ac684 - (40 minutes ago) Add new file: new_script3.py - Will
```

(It's worth noting that although the first output makes it appear that the local `develop` branch includes the `3feff89` commit, the second output shows that it is in fact on a separate branch.)

Let's have a look at the `git status` output:

```
$ git status
On branch develop
Your branch and 'origin/develop' have diverged,
and have 1 and 2 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean
```

OK, no surprises there. Let's now do the rebase and see what happens:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git pull --rebase
First, rewinding head to replay your work on top of it...
Applying: Add new file: module.py

$ git push
Counting objects: 2, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 246 bytes | 246.00 KiB/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote:
remote: To create a merge request for develop, visit:
remote:   https://gitlab.com/OzNetNerd/git-tutorial/merge_requests/new?merge_request%5Bsource_branch%5D=develop
remote:
To https://gitlab.com/OzNetNerd/git-tutorial.git
   9ec5884..b50dab5  develop -> develop
```

And finally, let's take a look at our `git log` outputs:

```
Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3
b50dab5 - (28 minutes ago) Add new file: module.py - Will (HEAD -> develop, origin/develop)
9ec5884 - (28 minutes ago) Add new file: food.py - John
3feff89 - (28 minutes ago) Add new file: menu.py - John

Will@MainPC MINGW64 /f/Will/git-tutorial (develop)
$ git log --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all -3 --graph
* b50dab5 - (28 minutes ago) Add new file: module.py - Will (HEAD -> develop, origin/develop)
* 9ec5884 - (28 minutes ago) Add new file: food.py - John
* 3feff89 - (29 minutes ago) Add new file: menu.py - John
```

Wait a minute, what happened to our `git log` graph? Last time we checked it there was a diverge. And, why is our `module.py` commit at the tip of the branch? Shouldn't it be in the middle of John's two commits given that it was the second commit to be made out of the three?

These mysterious happenings are due to the way in which rebasing works. Because we rebased `origin/develop` onto our local `develop` branch, git removes commits, brings our `develop` branch into sync with the `origin/develop` branch and then re-applies our commits to the tip of the newly synced branch.

As we saw above, this results in two things:

*   A linear log (as was the case with a Fast Forward merge)
*   Our local commits, regardless of whether they were made before and/or after the remote's commits, will be re-committed as if they were always committed after the origin's commits