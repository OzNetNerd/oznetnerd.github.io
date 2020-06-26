---
title: "Python: Covering all your bases with Coverage"
sub_heading: "Test all the things!"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- Python

tags:
- DevOps
- Python
- TDD
---

[Test Driven Development (TDD) in Python](/2020/06/15/test-driven-development-in-python/) touched on TDD and pytest. Then in [Taking pytest for a test drive](/2020/06/16/taking-pytest-for-a-test-drive/) we dived deeper on both subjects. 

Now that we've got those two covered off, it's time to talk about [pytest-cov](https://github.com/pytest-dev/pytest-cov). In a nutshell, pytest-cov tells us how much of our code base is covered by our tests. This is useful because as our project grows, we're able to maintain/enforce a minimum amount of coverage. 

## Installation

We'll use the same directory structure as [last time](/2020/06/15/test-driven-development-in-python/). Next, we need to set up our `requirements-dev.txt` file:

```
pytest
pytest-cov
-r requirements.txt
```

Now we need to write the contents of our `setup.py` file. As outlined in the [pytest documentation](https://docs.pytest.org/en/latest/goodpractices.html), the following is necessary at a minimum:

{% highlight python linenos %}
from setuptools import setup, find_packages

setup(name="DemoApp", packages=find_packages())
{% endhighlight %}

Let's now install our dependencies and set up our package for testing:

```
pip3 install -r requirements-dev.txt
pip install -e .
```

OK now we're ready to go! Let's start with a basic example. In `app.py`, paste the following code:

{% highlight python linenos %}
def hello(name):
    return f"Hello {name}!"
{% endhighlight %}

And now paste the following code into `tests/app_test.py`:

{% highlight python linenos %}
from src.app import *

def test_hello():
    greeting = hello("John")
    assert greeting == "Hello John!"
{% endhighlight %}


Now it's time to run our test. Let's see what happens:

```
$ pytest --cov=src
=========================== test session starts ===========================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1
rootdir: /home/wrobinson/Development/python-tdd
plugins: cov-2.10.0
collected 1 item                                                          

tests/app_test.py .                                                 [100%]

----------- coverage: platform linux, python 3.8.0-final-0 -----------
Name         Stmts   Miss  Cover
--------------------------------
src/app.py       2      0   100%


============================ 1 passed in 0.03s ============================
```

Perfect! We've got the coveted 100% coverage. Well to be honest, it's wasn't that hard given that our app is so small :) But it's a win nevertheless!

Say we wanted to modify our function to check for blank usernames, like so:

{% highlight python linenos %}
def hello(name):
    if not name:
        return "Please enter your name."
    
    return f"Hello {name}!"
{% endhighlight %}

What do you think will happen when we re-run our test? Let's see if you're right...

```
$ pytest --cov=src
=========================== test session starts ===========================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1
rootdir: /home/wrobinson/Development/python-tdd
plugins: cov-2.10.0
collected 1 item                                                          

tests/app_test.py .                                                 [100%]

----------- coverage: platform linux, python 3.8.0-final-0 -----------
Name         Stmts   Miss  Cover
--------------------------------
src/app.py       4      1    75%


============================ 1 passed in 0.05s ============================
```

Our test still passes, but our coverage has dropped. This is because we're not testing our `if` statement's section of code. 

This is very valuable information. If there were a bug in that piece of code, we'd be none the wiser. Let's go ahead and remedy that now.

Add the following test to `tests/app_test.py`:

{% highlight python linenos %}
def test_hello_no_name():
    greeting = hello("")
    assert greeting == "Please enter your name."
{% endhighlight %}

Now let's see what happens when we re-run our tests:

```
$ pytest --cov=src
=========================== test session starts ===========================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1
rootdir: /home/wrobinson/Development/python-tdd
plugins: cov-2.10.0
collected 2 items                                                         

tests/app_test.py ..                                                [100%]

----------- coverage: platform linux, python 3.8.0-final-0 -----------
Name         Stmts   Miss  Cover
--------------------------------
src/app.py       4      0   100%


============================ 2 passed in 0.05s ============================
```

We're back to 100% coverage!

## Closing thoughts

It's important to note that Coverage doesn't guarantee that our code is flawless. However, proper testing and good coverage does enable us to reduce bugs considerably.

If you're not using pytest and/or coverage already, I highly recommend you give them a try.
