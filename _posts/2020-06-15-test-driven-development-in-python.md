---
title: "Test Driven Development (TDD) in Python"
sub_heading: "Test Driven Development (TDD): Feels slow, is fast"

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

Have you heard of Test Driven Development (TDD)? If you haven't, you're in for a real treat.

TDD is a development methodology. Before writing a new function or method, a dev will write a "test" for it. The test's job is to ensure the code produces the correct output.

As the code does not yet exist, the test will of course fail the first time it is run. The dev then gets to work writing the code. Once they're done, they re-run the test. If the test passes, they get rest assured their code works as expected.

**Note:** There are numerous test frameworks in Python. PyTest is one of the most popular options and is used in this post.
{: .notice}

## TDD with PyTest

Let's look at a basic example. Let's say we want to write a function which takes two integers as parameters, and adds them together. 

First, we write our test function:

{% highlight python linenos %}
def test_add_nums():
    assert add_nums(1, 1) == 2
{% endhighlight %}

As we can see, we're passing in two integers as parameters. We're then using an `assert`ing that the result should be `2`. Next, we create the actual function:

{% highlight python linenos %}
def add_nums(num1, num2):
    total = num1 + num2

    return total
{% endhighlight %}    

Now we run PyTest to ensure the code is sound:

```
============================= test session starts =============================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1 -- /home/wrobinson/Development/python-tdd/venv/bin/python
cachedir: .pytest_cache
rootdir: /home/wrobinson/Development/python-tdd
collected 1 item                                                              

tests/add_test.py::test_add_nums PASSED                               [100%]

============================== 1 passed in 0.01s ==============================
```

Great! Our function works as expected. However, given that the above example is so simple, you'd be forgiven for not yet seeing the value of TDD.

Let's say we wanted to add additional functionality to `add_nums`:

{% highlight python linenos %}
def add_nums(num1, num2):
    num1 = num1 + num2
    if num1 > num2:
        print("num1 is greater than num2")
    elif num1 < num2:
        print("num2 is greater than num2")
    elif num1 == num2:
        print("num1 and num2 are equal")
    total = num1 + num2

    return total
{% endhighlight %}  

Our function is still very straightforward, isn't it? Well actually, we've just introduced a bug accidentally. If neither we or our peer reviewers notice it, we've just broken our application.

Thankfully TDD has our back though. By running our tests, we'll be able to catch our error:

 ```
$ pytest tests -v
============================= test session starts =============================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1 -- /home/wrobinson/Development/python-tdd/venv/bin/python
cachedir: .pytest_cache
rootdir: /home/wrobinson/Development/python-tdd
collected 1 item                                                              

tests/add_test.py::test_add_nums FAILED                               [100%]

================================== FAILURES ===================================
________________________________ test_add_nums ________________________________

    def test_add_nums():
>       assert add_nums(1, 1) == 2
E       assert 3 == 2
E         +3
E         -2

tests/add_test.py:30: AssertionError
---------------------------- Captured stdout call -----------------------------
num1 is greater than num2
=========================== short test summary info ===========================
FAILED tests/add_test.py::test_add_nums - assert 3 == 2
============================== 1 failed in 0.08s ==============================
```

Examining the output above, we see the expected output is `2`, but it is now `3`. Given that the test passed previously, we know that it's a real bug.

We can now get to work fixing it. Once done, we can re-run our tests. If we get the all clear, we can then integrate our changes into the mainline branch.  

## Closing thoughts

Applications grow and evolves over time. As they do, it is inevitable that the number of bugs we create increases. Furthermore, if we're refactoring our code in order to reduce technical debt, we've got yet another avenue for bugs to sneak in. 

TDD is fantastic approach to ensuring things work the same way before and after changes are made. It's automated approach to testing means that we can use it on all of our projects, no matter how large they are. 