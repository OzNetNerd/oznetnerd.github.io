---
title: Installing & using Python virtualenv
sub_heading: "A Python developer's best friend"
redirect_from: /installing-using-python-virtualenv/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python

tags:
- Python
---
virtualenv, as the name suggests, creates virtual Python environments. If you're familiar with server virtualisation, virtualenv acts in a similar fashion to virtual machines in that the environments share the same physical hardware, but they're completely separated from one another.

virtualenv is very useful for when you're working on multiple projects or using applications that require different versions of the same module. In this post I'll demonstrate how to install and usage of virtualenv on Windows, though the process is similar for Linux too.

If you haven't already, install virtualenv like so:

```
C:\>pip install virtualenv
Collecting virtualenv
Downloading virtualenv-15.1.0-py2.py3-none-any.whl (1.8MB)
100% |################################| 1.8MB 461kB/s
Installing collected packages: virtualenv
Successfully installed virtualenv-15.1.0

C:\>
```

Next, create a directory to store your virtualenv(s), and then create one. In the example below, my virtualenv is called test_env:

```
F:\Will>mkdir virtualenv
F:\Will>cd virtualenv
F:\Will\virtualenv>virtualenv test_env --no-site-packages
New python executable in F:\Will\virtualenv\test_env\Scripts\python.exe
Installing setuptools, pip, wheel...done.
```

Next, change to the virtualenv\Scripts directory and issue the activate command:

```
F:\Will\virtualenv>cd test_env\Scripts
F:\Will\virtualenv\test_env\Scripts>activate
(test_env) F:\Will\virtualenv\test_env\Scripts>
```

Once you have done this, your prompt will be prefixed with the name of your environment, e.g (test_env). This let's you know that you're now in a completely separate environment to all of your other environments, including your original Python installation.

If you'd like to exit out of your environment, issue the deactivate command:

```
(test_env) F:\Will\virtualenv\test_env\Scripts>deactivate
```