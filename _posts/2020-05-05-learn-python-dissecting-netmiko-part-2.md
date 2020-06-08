---
title: "Learn Python: Dissecting Netmiko - Part 2"
sub_heading: "The best way to learn how to code is by reading someone else's code"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- Python

tags:
- DevOps
- Python
- Netmiko
- Network Automation
---

This is the second post in this series. If you missed Part 1, [you can find it here](/2020/05/04/learn-python-dissecting-netmiko-part-1/).

## Dissecting ConnectHandler

Let's continue our `ConnectHandler` investigation. When we [look at the code](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L259-L263), the first thing we  `if` statement:

{% highlight python linenos %}
if kwargs["device_type"] not in platforms:
    raise ValueError(
        "Unsupported device_type: "
        "currently supported platforms are: {}".format(platforms_str)
    )
{% endhighlight %}

The `if` statement tells us that we're looking for the `device_type` key in the `kwargs` dictionary. Once we find it, we then check if it exists in `platforms`.

But what happens if we provide an invalid `device_type`? Judging by the code, Netmiko should raises a `ValueError` exception and print an informative message. But let's take a look just to be sure:

Here's our code:

{% highlight python linenos %}
from netmiko import ConnectHandler

linux = {
    'device_type': 'This-Is-A-Fake-Device-Type',
    'host':   '127.0.0.1',
    'username': 'will',
}

ConnectHandler(**linux)
{% endhighlight %}

And here's our output:

{% highlight python linenos %}
Traceback (most recent call last):
  File "/home/wrobinson/Development/netmiko-blog/run.py", line 9, in <module>
    ConnectHandler(**linux)
  File "/home/wrobinson/Development/netmiko-blog/netmiko/ssh_dispatcher.py", line 262, in ConnectHandler
    "currently supported platforms are: {}".format(platforms_str)
ValueError: Unsupported device_type: currently supported platforms are: 
a10
accedian
alcatel_aos
alcatel_sros
apresia_aeos
arista_eos
aruba_os
avaya_ers
avaya_vsp
brocade_fastiron
brocade_netiron
brocade_nos

# and so on...

Process finished with exit code 1
{% endhighlight %}

Great! The code worked as expected. Before we move on though, what do you think will happen if we don't pass in a `device_type` at all? Let's have a look.

Here's our code:

{% highlight python linenos %}
from netmiko import ConnectHandler

linux = {
    'host':   '127.0.0.1',
    'username': 'will',
}

ConnectHandler(**linux)
{% endhighlight %}

And here's our output:

{% highlight python linenos %}
Traceback (most recent call last):
  File "/home/wrobinson/Development/netmiko-blog/run.py", line 9, in <module>
    ConnectHandler(**linux)
  File "/home/wrobinson/Development/netmiko-blog/netmiko/ssh_dispatcher.py", line 259, in ConnectHandler
    if kwargs["device_type"] not in platforms:
KeyError: 'device_type'

Process finished with exit code 1
{% endhighlight %}

This time we get a `KeyError` exception. That's because Netmiko is referencing a key (`device_type`) that doesn't exist in our dictionary (`kwargs`). 

Now, this error message isn't very user friendly. What can we do to clean it up? If your answer is a `try/except` block, you're right!

Here's our code:

{% highlight python linenos %}
from netmiko import ConnectHandler
import sys

linux = {
    'host':   '127.0.0.1',
    'username': 'will',
}

try:
    ConnectHandler(**linux)

except KeyError:
    sys.exit('Error: "device_type" must be passed to ConnectHandler')
{% endhighlight %}

And here's our output:

{% highlight python linenos %}
Error: "device_type" must be passed to ConnectHandler

Process finished with exit code 1
{% endhighlight %}

Excellent! That's much nicer, isn't it?

Having a look at the above code raises two questions:

1. As mentioned above, we know that Netmiko checks if our `device_type` is listed in the `platforms` variable. But where does `platforms` come from?
2. We can see that the exception message prints the `platforms_str` variable. But where does ``platforms_str`` come from?

The answers are quite involved, so let's cover them off in the next post in this series.

## Review

Before we wrap up, let's first review what we've learned in this post:

* Netmiko will raise a `ValueError` if we pass in an invalid `device_type`
* We can "catch" exceptions using `try/except` blocks
  * After catching an exception, we can control what happens next
* Trying to access a non-existant dictionary key (e.g `kwargs["invalid_key"]`) results in a `KeyError` exception being raised
  * *Bonus points:* If you want to return a default value when a key doesn't exist, you could use `get` instead. e.g `kwargs.get("device_type", "linux")`. Using this method, `linux` will be returned instead of a `KeyError` exception being raised