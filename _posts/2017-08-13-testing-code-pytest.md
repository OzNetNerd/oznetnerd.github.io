---
title: Testing your code with pytest
sub_heading: "Testing, testing... 1, 2, 3"
redirect_from: /testing-code-pytest/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python
- DevOps

tags:
- Python
- Coding
- CI/CD
- DevOps
---
If you're fairly new to coding chances are you've run into an issue where you make a minor change in one place, and then end up breaking your script in another place. In order to find out what went wrong you start adding `print` statements all over the place to _debug_ your code.

While it sound like a good idea, what you're actually doing is relying on Python to tell you when you've made a syntactical error. However, what if your syntax is find, but your code is incorrect?

For example, say you accidentally changed your `addition` function to a multiplication function by replacing the `+` with a `*`:

```ptyhon
def addition(a, b):
    return a * b
```

Python isn't going to tell you you've made an error because your syntax is perfectly legal. The only way you'll know that there's a problem is when you see the result posted from your script. If the result goes unnoticed though, the bug will remain until someone does notice.

Once you've realised there is a problem, the `print` method mentioned at the beginning of this post will help you track down the error, however, it can be very time consuming if this function is part of large script. And, if you were to make the same mistake again, you'd need to repeat the same time consuming process again.

It's safe to say that I found myself in this situation many times when I first started coding. Just when I was about to begin pulling my hair out I thought to myself, "there has to be a better way!". And sure enough, there is.

While **unittest** is part of the Python standard library, I much prefer **py.test** as it is much cleaner to read and write, and is very powerful too.

## Installation

As with just about everything with Python, installation is easy as:

```
sudo pip install pytest
```

##  pytest Basics

In a nutshell, you use pytest to ensure that your code produces the correct results. Let's take a look at [**this example**](https://docs.pytest.org/en/latest/assert.html#assert) in the pytest documentation:

```python
# content of test_assert1.py
def f():
    return 3

def test_function():
    assert f() == 4
```

Here we see two functions:

*   `f()` : Returns the integer 3.
*   `test_function`() :  assert s that `f()`  will return 4.

Because the `f()` returns a result that is different to the test assertion, **pytest** will alert us to this:

```
$ pytest test_assert1.py
======= test session starts ========
platform linux -- Python 3.x.y, pytest-3.x.y, py-1.x.y, pluggy-0.x.y
rootdir: $REGENDOC_TMPDIR, inifile:
collected 1 item

test_assert1.py F

======= FAILURES ========
_______ test_function ________

    def test_function():
>       assert f() == 4
E       assert 3 == 4
E        +  where 3 = f()

test_assert1.py:5: AssertionError
======= 1 failed in 0.12 seconds ========
```

Making minor modifications to this code enables us to create a test for our `addition` function:

```
def addition(a, b):
    return a * b

def test_addition():
    assert addition(4, 5) == 9
```

Doing so produces the following results:

```
F:\Will>pytest -v test_addition.py
============================= test session starts =============================
platform win32 -- Python 2.7.13, pytest-3.1.2, py-1.4.34, pluggy-0.4.0 -- c:\python27\python.exe
cachedir: .cache
rootdir: F:\Will, inifile:
plugins: cov-2.5.1
collected 1 items

test_addition.py::test_addition FAILED

================================== FAILURES ===================================
________________________________ test_addition ________________________________

    def test_addition():
>       assert addition(4, 5) == 9
E       assert 20 == 9
E        +  where 20 = addition(4, 5)

test_addition.py:5: AssertionError
========================== 1 failed in 0.03 seconds ===========================
```

And there you have it! Not only do we now know that your code is syntactically correct, but we also know that it is producing the results we expected. If someone makes a change to the function and the wrong figure is produced as a result, pytest will alert you to it.

## Conclusion

Using this technique to automate unit testing will save you a massive amount of time because it's not only automatic, but it tells you exactly where the error is. No longer will you need to include temporary `print` statements in order to find the source of an error, only to remove them once you've fixed the error, but then re-add them again if the error re-occurs. pytest is a clean, elegant and permanent solution.

**Update:** If you enjoy using pytest, you might also be intersted in my [**Allure2: A GUI for your code tests post**](/allure2-gui-code-tests/) too.