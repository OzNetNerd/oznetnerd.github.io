---
title: "Windows: Accessing Python & pip from anywhere"
sub_heading: "Global access FTW!"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
  - Windows
  - Python

tags:
  - Windows
  - Python
---

When you install Python on Windows, you may find that you cannot access it and/or pip from anywhere on the commandline. For example, you find that you need to navigate to C:\\Python27\\Scripts in order to use pip. To enable access to them from anywhere on the commandline, issue the following commands:

```
setx path "%path%;C:\\Python27;"
setx path "%path%;C:\\Python27\\Scripts;"
```