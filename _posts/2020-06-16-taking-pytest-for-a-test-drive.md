---
title: "Taking pytest for a test drive"
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
- TDD
---

In the [previous post](/2020/06/15/test-driven-development-in-python/) we got a glimpse of pytest. In this post, we'll be diving a little deeper. To do this, we'll be build a basic Flask app.
 
Before we start coding though, let's first set up our directory structure. Thankfully, pytest gives us a few examples in their [Directory Structure](https://docs.pytest.org/en/reorganize-docs/new-docs/user/directory_structure.html)  and [Good Practices](https://docs.pytest.org/en/latest/goodpractices.html) pages. For the purposes of this post, we'll use this structure:
 
 ```
.
├── requirements-dev.txt
├── requirements.txt
├── setup.py
├── src
│   └── app.py
└── tests
    ├── app_test.py
    └── conftest.py
```

Now that our structure is done, we need to fill in each of the files.

In the `requirements.txt` file, we'll add `flask` as it is a dependency for our project:

```
flask
```
 
In the `requirements-dev.txt` file, we'll add the following two lines. This makes it easy for devs to get started:

```
pytest
-r requirements.txt
```

As we're devs ourselves, we'll need to install these dependencies. To do that, we issue the following command:

```
pip3 install -r requirements-dev.txt
```

Now we need to write the contents of our `setup.py` file. As outlined in the [pytest documentation](https://docs.pytest.org/en/latest/goodpractices.html), the following is necessary at a minimum:

{% highlight python linenos %}
from setuptools import setup, find_packages

setup(name="FlaskApp", packages=find_packages())
{% endhighlight %}

Once that's done, we need to "install" our app in "editable" mode. We do this by issuing the following command:

```
pip install -e .
```

Now that we've got all the prep out of the way, we're ready to start coding our app. Let's put the following code in `app.py`: 

{% highlight python linenos %}
from flask import Flask

app = Flask(__name__)


@app.route('/')
def index():
    return 'OK'
{% endhighlight %}

To make sure our app works, let's run the following commands:

```
FLASK_APP=src/app.py flask run
```

When we browse to `127.0.0.1:5000`, we see an "OK" message returned. Great, it works!

Let's now get to work on setting up testing for our app. 

**Note:** The inspiration for our test app comes from the [flask documentation](https://flask.palletsprojects.com/en/1.1.x/testing/).
{: .notice}

In `conftest.py` add the following:

{% highlight python linenos %}
from src.app import app
import pytest


@pytest.fixture
def client():
    app.testing = True
    app.config['DEBUG'] = True
    with app.test_client() as client:
        yield client
{% endhighlight %}

See [this page](https://docs.pytest.org/en/2.7.3/plugins.html) for more information on `conftest.py`.
{: .notice}

We've doing a few things in this block of code, so let's pause for a moment and analyse it:

1. We're using the [`test_client()` method](https://flask.palletsprojects.com/en/1.1.x/api/#flask.Flask.test_client) - this enables us to test our Flask app 

2. We're then using a `yield` - this ensures our client stays up while we're using it for testing

3. Finally, we've wrapped the whole thing a [`fixture`](https://docs.pytest.org/en/stable/fixture.html) - this enables us to [use the client in our tests](https://docs.pytest.org/en/latest/fixture.html#conftest-py-sharing-fixture-functions), as we'll see in a moment  

In `app_test.py`, we add the following:

{% highlight python linenos %}
def test_index(client):
    r = client.get('/')
    assert r.status == '200 OK'
{% endhighlight %}

As you can see, we've passed the `client` fixture to our `test_index` test. We're then using the client's `get` method to call our `/` route. This results in a [response_class](https://flask.palletsprojects.com/en/1.1.x/api/#response-objects) object being returned. We then `assert` that the [status](https://flask.palletsprojects.com/en/1.1.x/api/#flask.Response.status) of the response to make sure it's `200 OK`.

The `assert` is what makes or breaks our test. If the assertion is correct, the test passes. Otherwise it fails.

It's now time to run our test:

```
$ pytest -v
=============================== test session starts ================================
platform linux -- Python 3.8.0, pytest-5.4.3, py-1.8.1, pluggy-0.13.1 -- /home/wrobinson/Development/python-tdd/venv/bin/python
cachedir: .pytest_cache
rootdir: /home/wrobinson/Development/python-tdd
collected 1 item                                                                   

tests/app_test.py::test_index PASSED                                         [100%]

================================ 1 passed in 0.02s =================================
```