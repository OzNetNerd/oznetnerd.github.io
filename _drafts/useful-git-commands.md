---
title: "Useful git commands"
# sub_heading: ""

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

# categories:
#   - TBA

# tags:
#   - TBA
---

diff two branches - shows file contents

```
$ git diff uat-cf-lambda..iam-aws-service-lambda
```

diff two branches - shows filenames only

```
$ git diff uat-cf-lambda..iam-aws-service-lambda --name-only
```

copy files from one branch/commit to another - https://stackoverflow.com/questions/307579/how-do-i-copy-a-version-of-a-single-file-from-one-git-branch-to-another

```
git show commit_id:path/to/file > path/to/file
```

git diff branches - https://stackoverflow.com/questions/9834689/comparing-two-branches-in-git

other git diff - https://stackoverflow.com/questions/17244471/see-diff-between-current-state-and-last-commit

read a file from another branch - http://gitready.com/intermediate/2009/02/27/get-a-file-from-a-specific-revision.html

```
git show iam-aws-service-lambda:./cwt-uat.tf
```

# find commit in a branch that differ from another, e.g comapre branch to master - 
# https://stackoverflow.com/questions/14848274/git-log-to-get-commits-only-for-a-specific-branch
# https://stackoverflow.com/questions/13965391/how-do-i-see-the-commit-differences-between-branches-in-git
# https://stackoverflow.com/questions/822811/showing-which-files-have-changed-between-two-revisions
# https://stackoverflow.com/questions/13965391/how-do-i-see-the-commit-differences-between-branches-in-git

```
git cherry -v develop mybranch
```
# conflict filenames - https://stackoverflow.com/questions/3065650/whats-the-simplest-way-to-get-a-list-of-conflicted-files

http://nicolasgallagher.com/git-checkout-specific-files-from-another-branch/