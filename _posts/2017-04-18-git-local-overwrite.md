---
title: Git Local Overwrite
sub_heading: "How to overwrite local files"
redirect_from: /git-local-overwrite/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
---
If your local repository is behind that of the remote repository and your locally tracked files differ from those of the remote repository, you will encounter an error. Performing the steps will result in the following:

*   If the locally tracked files **exist** on the remote repository, the remote files will overwrite the local files.
*   If the locally tracked files **do not exist** on the remote repository, they will be removed from your local repository.
*   If the local files **are not being tracked**, they will be left intact.

## Steps

Here is an example of the error you will see:

```
will@ubuntu:~/wip$ git push origin master
To git@gitlab.com:OzNetNerd/wip.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@gitlab.com:OzNetNerd/wip.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

If you then try to do a pull, you'll end up with this error:

```
will@ubuntu:~/wip$ git pull
remote: Counting objects: 42, done.
remote: Compressing objects: 100% (35/35), done.
remote: Total 42 (delta 20), reused 1 (delta 0)
Unpacking objects: 100% (42/42), done.
From gitlab.com:OzNetNerd/wip
   7aa408e..b5f74f7  master     -> origin/master
Updating 7aa408e..b5f74f7
error: Your local changes to the following files would be overwritten by merge:
	mvp/mvp-if_statement.yml
	roles/routers/tasks/main.yml
Please, commit your changes or stash them before you can merge.
Aborting
```

If you're happy to lose your locally tracked files, you can re-sync with the remote repository by issuing the following commands:

```
will@ubuntu:~/wip$ git fetch origin master
Fetching origin
will@ubuntu:~/wip$ git reset --hard origin/master
HEAD is now at b5f74f7 sync
will@ubuntu:~/wip$ 
will@ubuntu:~/wip$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
```