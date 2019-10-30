---
title: Getting started with git
sub_heading: "Getting good with Git"
redirect_from: /getting-started-with-git/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
---
## What is git?

[**Wikipedia**](https://en.wikipedia.org/wiki/Git) has a great answer for this question:

_Git is a version control system for tracking changes in computer files and coordinating work on those files among multiple people. It is primarily used for source code management in software development, but it can be used to keep track of changes in any set of files. As a distributed revision control system it is aimed at speed, data integrity, and support for distributed, non-linear workflows._

OK cool, now that we know what is git let's now take a look at git repositories.

## What is a Repository (repo)?

A repository is receptacle for files which are part of a project. Each project should be stored in a separate repository so that their files are kept separate, access to them can be administered separately, etc.

## Creating a new repo

When you create a new repo using a git server provided by the likes of GitHub and GitLab, you will be given a few options to help you get started. One of these options is as follows:

```
git clone git@gitlab.com:OzNetNerd/git-tutorial.git
cd git-tutorial
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

These commands are used to:

1.  Clone the repo from the remote git server (gitlab.com in this case) to our machine
2.  Creates a new file, README.md, on our machine
3.  Commits the file to our local repo, which resides on our machine
4.  Pushes the commit to the repo hosted on the remote git server

In other words, only the 4th step affects our remote repo. All other steps affect only our machine. This is important to note because without the last step, the following occurs:

1.  If our hard drive fails, our changes would be lost too
2.  No one can see our changes and therefore they cannot benefit from our contributions
3.  The longer we wait before doing the last step, the higher our chance are of having our changes conflict with other peoples'

I'll cover off these points in the next section, but for now let's go ahead and issue the commands GitLab gave us:

```
will@ubuntu:/tmp$ git clone git@gitlab.com:OzNetNerd/git-tutorial.git
Cloning into 'git-tutorial'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
will@ubuntu:/tmp$ cd git-tutorial
will@ubuntu:/tmp/git-tutorial$ touch README.md
will@ubuntu:/tmp/git-tutorial$ git add README.md
will@ubuntu:/tmp/git-tutorial$ git commit -m "add README"
[master (root-commit) dbc082d] add README
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md
will@ubuntu:/tmp/git-tutorial$ git push -u origin master
Counting objects: 3, done.
Writing objects: 100% (3/3), 220 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@gitlab.com:OzNetNerd/git-tutorial.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

This output raises more questions! Newcomers may be wondering:

*   What is a push?
*   What is a branch?
*   What is master?
*   What is origin and remote?

## What is a push?

As mentioned in the preceding section of this post, Steps 1 through 3 affected only our local machine. Once we're ready to share our work with others, we need to send our changes to the git server. This is known as a push.

This therefore means that:

1.  We're OK if our hard drive fails because our updates have now been sent to the remote git repo
2.  As the changes are on the remote server, others can now see and interacat with our changes
3.  By pushing our changes in a timely manner, we will be able to avoid conflicts

## What is a branch?

As mentioned earlier in this post, each repo should be dedicated to one project. However, what if you want to make modifications (e,g bug fixes and new features), to that project? You don't want to make those modifications live until you've had a chance to test them thoroughly otherwise you'll likely be adding more bugs than you're fixing.

The solution to this is problem is using a branch. By creating a new branch which is based off our production branch (see **"What is master?"**), we've effectively got our own, separate copy of the project that we can do with what we please. We can add, delete and modify files and then push them to our branch without affecting the production branch.

Once we're happy with the modifications we've made on our branch, we can then integrate them into the production branch and then delete our branch if we no longer have a use for it.

Some use cases for branches are:

*   **Bug fixes:** Regardless of how simple a bug fix seems, it's always a good idea to test the solution before putting it into production. By creating a temporary bug fix branch, we can do just that. Then, Once we're happy that the fix achieves the desired result and does not breach anything else, we can integrate them it into production
*   **New Features:** Adding a new feature to a project can be a time consuming process. As we don't want to release new features before they're ready, we can create "Feature Branches" (a fancy name for a branch which is being used to work on a new feature). Once the feature has been tested and is working, we can integrate it into the production branch
*   **Ideas: **Similar to the previous point, we can create branches to test new ideas. For example, we may want to test a performance optimisation technique. If the idea works, we can integrate it into production, if it doesn't we can delete the branch without a second thought
*   See [**Changing history, or How to Git pretty**](http://justinhileman.info/article/changing-history/) and [**A successful Git branching model**](http://nvie.com/posts/a-successful-git-branching-model/) for some great ideas on how to use branching

To reiterate, a branch is a safe place for us to do anything we want without affecting production. We could delete the entire project from our branch and push it to GitLab and we still won't affect production, purely because it resides in a different branch.

For more information, see the git book's branch [**Branches in a Nutshell**](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) and Atlassian's [**Using Branches**](https://www.atlassian.com/git/tutorials/using-branches) documentation.

## What is master?

master is the name of the branch which is created by default. Some use it as the "production" branch while others rename it to something more meaningful (e.g devel  or development).

## What is remote and origin?

The last line of the output we saw earlier in this post says:

```
Branch master set up to track remote branch master from origin.
```

This is git telling us that it has created a **local branch** on our machine called master and it is tracking the progress of a **remote branch **named master which is stored on origin.

This might sound like a lot to take in, but is actually very straightforward. Let's use what we've learned in this post to help us digest this output:

1.  **master branch**: We know that the master branch is the name of the branch which is created by default. However, we've now just learned that there's one master branch which is stored **locally** on our machine which is **tracking** a master branch which is stored **remotely**. What does that mean?!?
2.  **Local & Remote branches:** As covered previously, we use the push to sync our **local** changes with the **remote** git server. This is done by mapping our **local** branches to our **remote** branches, in other words, we have our **local** branch **track** the **remote** branch
3.  **Origin:** Is the default name for the remote git repo

Before we move onto the conclusion of this post, let's discuss the second point above a little further. Because the **local** and **remote** branches are two separate entities, you can make changes to one without affecting the other. Having said this, as discussed in [**Git: Keeping in sync**](/git-keeping-sync/) post, they must be in sync before a push can be done successfully.

One other thing I'd like to mention is that you cannot actually edit **remote** branches directly. The only way to update their contents is by using the push process mentioned in the previous paragraph.

## Summary

Let's finish this post off with a quick recap:

1.  Each individual project should be kept in a separate repo
2.  The master branch is created by default and is used by some as the production branch while renamed to something more applicable by others
3.  We can avoid making changes to production applications by creating a **branch** and making our modifications on it. Once they're ready to be shared with the rest of the team, we can merge them into the production branch
4.  **origin** is the default name for the **remote** git repo
5.  **Local** branches have corresponding **remote** branches. They're named similarly by default, e.g master and origin/master respectively
6.  In order to send our changes to the **remote** git repo, we use the push command on our **local** branch

_For more information, take a look at [**this section**](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) of the git book._