---
title: "Learn Python: Dissecting Netmiko - Part 1"
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

In the not too distant past, I was a network engineer. In fact, it's my interest in network automation that led me to DevOps, but I digress. During my network engineering days, my favourite tools were Ansible and [Netmiko](https://github.com/ktbyers/netmiko). Given how heavily I used Netmiko, I really wanted to give back to the project and the community. There was one "slight" problem though. My coding skills weren't great. 

However, with the help of [Kirk Byers](https://pynet.twb-tech.com/) (the creator of Netmiko), I came to realise that the project is actually very straightforward. Furthermore, I picked up a lot of valuable tips and tricks along the way. And that's what this series of posts is all about.

__The best way to learn how to code is by reading someone else's code.__ So without further ado, let's get started.

## Where do we start?

Netmiko's README provides us with a ["Getting Started"](https://github.com/ktbyers/netmiko#getting-started) snippet:

```
from netmiko import ConnectHandler

cisco_881 = {
    'device_type': 'cisco_ios',
    'host':   '10.10.10.10',
    'username': 'test',
    'password': 'password',
    'port' : 8022,          # optional, defaults to 22
    'secret': 'secret',     # optional, defaults to ''
}
```

This gives us our starting point. In order to understand how Netmiko works, we need to find where `ConnectHandler` is defined. We can see by the `from netmiko import ConnectHandler` import statement, it resides in the root level of the `netmiko` package.

## Finding ConnectHandler

When we look at the [netmiko package](https://github.com/ktbyers/netmiko/tree/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko), the function is nowhere to be found. That's because it's actually "hiding" in the [`__init__.py` file](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/__init__.py#L7):

```
from netmiko.ssh_dispatcher import ConnectHandler
```

__ProTip:__ Importing modules using `__init__.py` enables you to write cleaner code.
{: .notice--info}

After having reviewed `__init__.py`, we now know that `ConnectHandler` actually resides in the [`ssh_dispatcher.py` file](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L257-L265):

```
def ConnectHandler(*args, **kwargs):
    """Factory function selects the proper class and creates object based on device_type."""
    if kwargs["device_type"] not in platforms:
        raise ValueError(
            "Unsupported device_type: "
            "currently supported platforms are: {}".format(platforms_str)
        )
    ConnectionClass = ssh_dispatcher(kwargs["device_type"])
    return ConnectionClass(*args, **kwargs)
```

It's easy to get discouraged when you see the amount of code in `ssh_dispatcher.py`, but don't worry. It's actually very straight forward as you'll see in a in this series of posts.

## Understanding ConnectHandler

Now that we've found the [`ConnectHandler` function](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L257), let's get to work analysing it. The first thing we see is it's docstring: 

*__Factory function__ selects the proper __class__ and creates object based on __device_type__.* 

Let's take a look at what all of this means.

### Factory Functions

OK, so what are Factory Functions? As explained by [Real Python](https://realpython.com/factory-method-python/):

*Instead of using a complex if/elif/else conditional structure to determine the concrete implementation, the application delegates that decision to a separate component that creates the concrete object. With this approach, the __application code is simplified, making it more reusable and easier to maintain.__*

Don't worry if that doesn't make sense. It will soon.

### Class

Next, we have __class__. This likely has something to do with the classes [being imported at the top](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L2-L83) of `ssh_dispatcher.py`:

```
"""Controls selection of proper class based on the device type."""
from netmiko.a10 import A10SSH
from netmiko.accedian import AccedianSSH
from netmiko.alcatel import AlcatelAosSSH
from netmiko.arista import AristaSSH, AristaTelnet
from netmiko.arista import AristaFileTransfer
from netmiko.apresia import ApresiaAeosSSH, ApresiaAeosTelnet
from netmiko.aruba import ArubaSSH
from netmiko.calix import CalixB6SSH, CalixB6Telnet
from netmiko.checkpoint import CheckPointGaiaSSH
from netmiko.ciena import CienaSaosSSH, CienaSaosTelnet, CienaSaosFileTransfer
from netmiko.cisco import CiscoAsaSSH, CiscoAsaFileTransfer
from netmiko.cisco import (
    CiscoIosSSH,
    CiscoIosFileTransfer,
    CiscoIosTelnet,
    CiscoIosSerial,

# and so on...
```

### device_type

Finally, we have __device_type__. Looking at the "Getting Started" snippet, we can see that `device_type` is a string which we pass in to `ConnectHandler`.

## ConnectHandler signature

OK, now that we understand the docstring, let's talk about the `ConnectHandler`'s signature:

```
ConnectHandler(*args, **kwargs)
```

 `*args` and `**kwargs` allow us to pass in a variable number of arguments and keyword arguments to the `ConnectHandler` function. This is handy because the arguments we'd need for Linux server likely differ from the ones we need for a network device. For example, a Linux server doesn't need an `enable` password, but some network devices do.

In a nutshell, `ConnectHandler` doesn't need to take into account the unique parameters of every type of router, switch, firewall, Linux server or any other device Netmiko connects to now or will connect to in the future. By using `*args` and `**kwargs`, we're able to cater for every single one of them without needing modify the code.  

## To be continued...

To avoid information overload, I'll end this post here. Before I do though, let's quickly review what we've learned.

1. Putting `import` statements in `__init__.py` enables us to write __cleaner code__
2. `ConnectHandler` is a factory function. This design pattern enables us to write __less code__
3. By accepting `*args` and `**kwargs` arguments, `ConnectHandler` is able to cater for every type of platforms

In the next post, we'll dive a lot deeper into the code and analyse every aspect of the `ConnectHandler` function.
