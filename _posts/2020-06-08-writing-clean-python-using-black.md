---
title: "Writing clean Python using Black"
sub_heading: "The value of consistent code styling cannot be overstated"
redirect_from: /2020/06/08/writing-clean-code-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- Python

tags:
- DevOps
- Python
---

There are two important methodologies which can be used to assist you in writing clean, maintainable code. They are:
1. Style Guides
2. Test Driven Development (TDD)

In this series of posts we'll cover what they are and how to set them up.

## Style Guides

If you've been coding for any amount of time, you've likely heard of [PEP8 - Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/). If you haven't, it's a set of contentions for writing code. When followed, it enables coders to write in a common style. By doing so, they are able to more easily understand each other's code.

To ensure that the guide is adhered to, several tools have been written which automate this process. To name a few:
* autopep8
* pylint
* flake8
* pep8
* pyflakes

While these are all great tools, they are highly configurable. While this may sound like a benefit, it can lead to disagreements. A simple example is, _should strings be enclosed in single `(')` or dobule `(")` quotation marks?_ A solution to this problem is [Black](https://github.com/psf/black). Black is:

_"The uncompromising Python code formatter. By using it, you agree to cede control over minutiae of hand-formatting. In return, Black gives you speed, determinism, and freedom from pycodestyle nagging about formatting. You will save time and mental energy for more important matters."_
 
Sounds great, doesn't it? Let's now look at how we use it.

**Note:** If you'd like to know more, Black's style guide can be [found here](https://black.readthedocs.io/en/stable/the_black_code_style.html). 
{: .notice}

## Setting up Black
### Installation

First, we need to create initalise our Git repo:

```
/tmp$ mkdir demo
/tmp/demo$ git init
Initialized empty Git repository in /tmp/demo/.git/
```

Next we need to create our `requirements-dev.txt` file:

```
/tmp/demo$ cat requirements-dev.txt
pre-commit
black
```

**Note:** Git pre-commit hooks enable us to run scripts before a commit is made. The `pre-commit` Python library makes deploying these hooks extremely simple.
{: .notice}

Now we need to install these packages:

```
pip install -r requirements-dev.txt --user
```

Next we'll need to set up our `.pre-commit-config.yaml` file as [described here](https://pre-commit.com/#quick-start). For example:

```
/tmp/demo$ cat .pre-commit-config.yaml 
repos:
  - repo: https://github.com/ambv/black
    rev: stable
    hooks:
    - id: black
      language_version: python3.7
```

Finally, to install our pre-commit into the `.git/` directory, we'll need to run the following command:

```
pre-commit install
```

### Testing

Create a file called `hello.py`, and add the following line:

```
print('hello')
```

Now commit the change. If Black was set up correctly, you'll see the following output:

```
/tmp/demo$ git commit -am 'update'
black....................................................................Failed
- hook id: black
- files were modified by this hook

reformatted hello.py
All done! ✨ 🍰 ✨
1 file reformatted.
```

From the output we can see that Black has reformatted our file. To see what it has changed, we can do a `git diff`:

```
/tmp/demo$ git diff
diff --git a/hello.py b/hello.py
index b376c99..11b15b1 100644
--- a/hello.py
+++ b/hello.py
@@ -1 +1 @@
-print('hello')
+print("hello")
```

We can see that Black changed our single quotes to doubles. To accept the change, we simply re-add the affected file(s) and re-do the commit:

```
/tmp/demo$ git add hello.py
/tmp/demo$ git commit -am 'Added hello file'
```

To verify that our commit was saved this time, we can check out git history:

```
/tmp/demo$ git log --oneline
12edeb9 (HEAD -> master) Added hello file
```

**Note:** We can have Black check our code without modifying by using the `--diff` flag, like so: `/tmp/demo$ black --diff .`
{: .notice}

### Modifying Black

While Black is an _"uncompromising Python code formatter"_, it actually does offer a little flexibility. For example, if we'd like to change Black's [88 character line length](https://black.readthedocs.io/en/stable/the_black_code_style.html#line-length), we can. We simply create a `pyproject.toml` file as [described here](https://black.readthedocs.io/en/stable/pyproject_toml.html). We then add the following to it:

```
[tool.black]
line-length = 120
```

### IDE Integration

While pre-commit hooks are a great "last line of defence", I'm not a fan of the two commit process we saw above. Furthermore, I also prefer immediate feedback. If you feel the same, then I highly recommend setting up an [IDE integration](https://black.readthedocs.io/en/stable/editor_integration.html).

As a PyCharm user, all I need to do is press `Control` + `S` and the code is updated right in front of my eyes.