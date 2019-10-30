---
title: 'Git: Effective branching using workflows'
sub_heading: "Using Git effectively"
redirect_from: /git-effective-branching-workflows/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
---
In the **[Getting started with git](/getting-started-with-git/)** we learned about local and remote branches (master and origin/master respectively), and in [**Git: Keeping in sync**](/git-keeping-sync/) we learned how to keep the two branches in sync. This is all great stuff, but if you're working in a team and/or on a serious project, using only the master branch is not a good idea. The reason being that if master is having development code pushed to it continuously, it will never be stable.

What you should do instead, at a minimum, is have two branches. For example, dev and master, where dev is used for features which are being developed and master is used for production code. Once the in-development features have been tested and are ready for production, they can then be merged into master. By employing this method master will always be in a stable state.

Let's not stop there though! If we've got multiple, unrelated features under development concurrently, the chances of them all being completed at the same time are slim. Therefore it wouldn't be safe to merge the dev branch into master each time a new feature was complete because the incomplete features may cause instability when integrated into production (the master branch). The solution to this issue is to put each feature into its own branch. This enables us to integrate them into master on a case by case basis as each feature is completed.

OK great, it sounds like we've got the perfect solution then. Oh wait, what about bug fixes? They're not really a feature so doing them in the feature branches wouldn't make sense. We definitely don't want to do them on the master branch because whenever we edit code there's always a chance that we'll break more things than we fix. Therefore the only suitable solution would be to use new branches when fixing bugs, and then merge them into master when appropriate.

## git workflows

What I've just alluded to is the need for a git workflow. A workflow is a structured way in which you develop and integrate your code. Workflows can consist of a small number of branches (e.g two branches as covered in the previous section of this post), or they can consist of several more branches.

There is a huge amount of information available that covers workflows in depth, so instead of reinventing the wheel, I'll provide links to the sources which I think you'll find useful:

*   [**GitLab Flow**](https://about.gitlab.com/2014/09/29/gitlab-flow/)
*   [**Simple Git workflow is simple**](https://www.atlassian.com/blog/archives/simple-git-workflow-simple)
*   [**Comparing Workflows**](https://www.atlassian.com/git/tutorials/comparing-workflows)
*   [**A successful Git branching model**](http://nvie.com/posts/a-successful-git-branching-model/)
*   [**Git Workflows for Pros: A Good Git Guide**](https://www.toptal.com/git/git-workflows-for-pros-a-good-git-guide)
*   [**GitHub Flow**](http://scottchacon.com/2011/08/31/github-flow.html)

There are a wide variety of workflows. Make sure you choose your workflows on a case by case basis as what works for some projects may not work for others.