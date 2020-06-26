---
title: "Learn Python: Dissecting Netmiko - Part 3"
sub_heading: "The best way to learn how to code is by reading someone else's code"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps
- Python
- NetOps
- Network Automation

tags:
- DevOps
- Python
- Netmiko
- NetOps
- Network Automation
---

This is the third post in this series. If you missed Part 2, [you can find it here](/2020/05/05/learn-python-dissecting-netmiko-part-2/).

## Dissecting platforms

Though it appears `platforms` is [only 2 lines](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L244-L245):

{% highlight python linenos %}
platforms = list(CLASS_MAPPER.keys())
platforms.sort()
{% endhighlight %}

There's actually a lot more to it. Let's take a look at what I mean.

1. First, we see it [refers to `CLASS_MAPPER`](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L244)
  {% highlight python linenos %}platforms = list(CLASS_MAPPER.keys()){% endhighlight %}
2. We then see `CLASS_MAPPER` [refers to `new_mapper`](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L200) 

  {% highlight python linenos %}CLASS_MAPPER = new_mapper{% endhighlight %}
3. `new_mapper` then [refers to `CLASS_MAPPER_BASE`](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L86-L179) 

To understand what's happening, we need to look at the above list in reverse order. Putting it visually, the flow looks like this:

{% highlight python linenos %}
CLASS_MAPPER_BASE => new_mapper => CLASS_MAPPER => platforms
{% endhighlight %}

Let's get started...

### Dissecting CLASS_MAPPER_BASE 

[`CLASS_MAPPER_BASE`](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L86-L179) is actually very straight forward. It's simply a dictionary which maps `device_type` strings to their classes. These classes were [imported at the top](https://github.com/ktbyers/netmiko/blob/211fd9da18b49acd65f390f722a460b55bc672e2/netmiko/ssh_dispatcher.py#L1-L83) of the file.

Here's what it looks like when the code is run:

{% highlight python linenos %}
from pprint import pprint

pprint(CLASS_MAPPER_BASE)
{'a10': <class 'netmiko.a10.a10_ssh.A10SSH'>,
 'accedian': <class 'netmiko.accedian.accedian_ssh.AccedianSSH'>,
 'alcatel_aos': <class 'netmiko.alcatel.alcatel_aos_ssh.AlcatelAosSSH'>,
 'alcatel_sros': <class 'netmiko.nokia.nokia_sros_ssh.NokiaSrosSSH'>,
 'apresia_aeos': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosSSH'>,
 'arista_eos': <class 'netmiko.arista.arista.AristaSSH'>,
 'aruba_os': <class 'netmiko.aruba.aruba_ssh.ArubaSSH'>,
 'avaya_ers': <class 'netmiko.extreme.extreme_ers_ssh.ExtremeErsSSH'>,
 'avaya_vsp': <class 'netmiko.extreme.extreme_vsp_ssh.ExtremeVspSSH'>,
 'brocade_fastiron': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironSSH'>,
 'brocade_netiron': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironSSH'>,
 'brocade_nos': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vdx': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vyos': <class 'netmiko.vyos.vyos_ssh.VyOSSSH'>,
 'calix_b6': <class 'netmiko.calix.calix_b6.CalixB6SSH'>,
 
 # and so on...
}
{% endhighlight %}

Now that we've got `CLASS_MAPPER_BASE` covered off, let's see what `new_mapper` does with it.

### Dissecting new_mapper

`new_mapper` is defined twice. The first time is for [`ssh` mappings](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L205). And the second time is for [file transfer](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L212) mappings. We're going to be investigating the SSH mapping code.  
{: .notice--info}

This piece of code runs a for loop against `CLASS_MAPPER_BASE`. In the loop we:
1.  [Extract](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L206) the key (`k`) and value (`v`) of each dictionary entry. For example:
    * `k` = `a10` 
    * `v` = `<class 'netmiko.a10.a10_ssh.A10SSH'>`
2. We then [add the key and value](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L214) to our `new_mapper` dictionary
3. Next we [append `_ssh`](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L215) to the key's name. For example:
    * `alt_key` =  `a10_ssh`
4. Finally, we [add the new key](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L216) and existing value to the `new_mapper` dictionary.

The result? We have two ways names which can be used to instrument an SSH connection to a platform. e.g `a10` and `a10_ssh` both call the `netmiko.a10.a10_ssh.A10SSH` class:

{% highlight python linenos %}
pprint(new_mapper)
{'a10': <class 'netmiko.a10.a10_ssh.A10SSH'>,
 'a10_ssh': <class 'netmiko.a10.a10_ssh.A10SSH'>,
 'accedian': <class 'netmiko.accedian.accedian_ssh.AccedianSSH'>,
 'accedian_ssh': <class 'netmiko.accedian.accedian_ssh.AccedianSSH'>,
 'alcatel_aos': <class 'netmiko.alcatel.alcatel_aos_ssh.AlcatelAosSSH'>,
 'alcatel_aos_ssh': <class 'netmiko.alcatel.alcatel_aos_ssh.AlcatelAosSSH'>,
 'alcatel_sros': <class 'netmiko.nokia.nokia_sros_ssh.NokiaSrosSSH'>,
 'alcatel_sros_ssh': <class 'netmiko.nokia.nokia_sros_ssh.NokiaSrosSSH'>,
 'apresia_aeos': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosSSH'>,
 'apresia_aeos_ssh': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosSSH'>,
 'arista_eos': <class 'netmiko.arista.arista.AristaSSH'>,
 'arista_eos_ssh': <class 'netmiko.arista.arista.AristaSSH'>,
 'aruba_os': <class 'netmiko.aruba.aruba_ssh.ArubaSSH'>,
 'aruba_os_ssh': <class 'netmiko.aruba.aruba_ssh.ArubaSSH'>,
 'avaya_ers': <class 'netmiko.extreme.extreme_ers_ssh.ExtremeErsSSH'>,
 'avaya_ers_ssh': <class 'netmiko.extreme.extreme_ers_ssh.ExtremeErsSSH'>,
 'avaya_vsp': <class 'netmiko.extreme.extreme_vsp_ssh.ExtremeVspSSH'>,
 'avaya_vsp_ssh': <class 'netmiko.extreme.extreme_vsp_ssh.ExtremeVspSSH'>,
 'brocade_fastiron': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironSSH'>,
 'brocade_fastiron_ssh': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironSSH'>,
 'brocade_netiron': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironSSH'>,
 'brocade_netiron_ssh': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironSSH'>,
 'brocade_nos': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_nos_ssh': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vdx': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vdx_ssh': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vyos': <class 'netmiko.vyos.vyos_ssh.VyOSSSH'>,
 'brocade_vyos_ssh': <class 'netmiko.vyos.vyos_ssh.VyOSSSH'>,
 'calix_b6': <class 'netmiko.calix.calix_b6.CalixB6SSH'>,
 
 # and so on...
{% endhighlight %}
 
You're probably wondering why we need two ways to reference the same class. The reason becomes apparent when we dissect `CLASS_MAPPER`.

### Dissecting CLASS_MAPPER

We can see that `CLASS_MAPPER` [starts life as __pointer__](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L210) to `new_mapper`.

It's very important to understand the difference between a  __pointer__ and a __copy__ of an object. If you don't know the difference, it's worth having a read of [this article](https://realpython.com/pointers-in-python/).
{: .notice--info}

Let's see what `CLASS_MAPPER` looks like now:

{% highlight python linenos %}
pprint(CLASS_MAPPER)
{'a10': <class 'netmiko.a10.a10_ssh.A10SSH'>,
 'a10_ssh': <class 'netmiko.a10.a10_ssh.A10SSH'>,
 'accedian': <class 'netmiko.accedian.accedian_ssh.AccedianSSH'>,
 'accedian_ssh': <class 'netmiko.accedian.accedian_ssh.AccedianSSH'>,
 'alcatel_aos': <class 'netmiko.alcatel.alcatel_aos_ssh.AlcatelAosSSH'>,
 'alcatel_aos_ssh': <class 'netmiko.alcatel.alcatel_aos_ssh.AlcatelAosSSH'>,
 'alcatel_sros': <class 'netmiko.nokia.nokia_sros_ssh.NokiaSrosSSH'>,
 'alcatel_sros_ssh': <class 'netmiko.nokia.nokia_sros_ssh.NokiaSrosSSH'>,
 'apresia_aeos': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosSSH'>,
 'apresia_aeos_ssh': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosSSH'>,
 'apresia_aeos_telnet': <class 'netmiko.apresia.apresia_aeos.ApresiaAeosTelnet'>,
 'arista_eos': <class 'netmiko.arista.arista.AristaSSH'>,
 'arista_eos_ssh': <class 'netmiko.arista.arista.AristaSSH'>,
 'arista_eos_telnet': <class 'netmiko.arista.arista.AristaTelnet'>,
 'aruba_os': <class 'netmiko.aruba.aruba_ssh.ArubaSSH'>,
 'aruba_os_ssh': <class 'netmiko.aruba.aruba_ssh.ArubaSSH'>,
 'autodetect': <class 'netmiko.terminal_server.terminal_server.TerminalServerSSH'>,
 'avaya_ers': <class 'netmiko.extreme.extreme_ers_ssh.ExtremeErsSSH'>,
 'avaya_ers_ssh': <class 'netmiko.extreme.extreme_ers_ssh.ExtremeErsSSH'>,
 'avaya_vsp': <class 'netmiko.extreme.extreme_vsp_ssh.ExtremeVspSSH'>,
 'avaya_vsp_ssh': <class 'netmiko.extreme.extreme_vsp_ssh.ExtremeVspSSH'>,
 'brocade_fastiron': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironSSH'>,
 'brocade_fastiron_ssh': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironSSH'>,
 'brocade_fastiron_telnet': <class 'netmiko.ruckus.ruckus_fastiron.RuckusFastironTelnet'>,
 'brocade_netiron': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironSSH'>,
 'brocade_netiron_ssh': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironSSH'>,
 'brocade_netiron_telnet': <class 'netmiko.extreme.extreme_netiron.ExtremeNetironTelnet'>,
 'brocade_nos': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_nos_ssh': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vdx': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vdx_ssh': <class 'netmiko.extreme.extreme_nos_ssh.ExtremeNosSSH'>,
 'brocade_vyos': <class 'netmiko.vyos.vyos_ssh.VyOSSSH'>,
 'brocade_vyos_ssh': <class 'netmiko.vyos.vyos_ssh.VyOSSSH'>,
 'calix_b6': <class 'netmiko.calix.calix_b6.CalixB6SSH'>,
 'calix_b6_ssh': <class 'netmiko.calix.calix_b6.CalixB6SSH'>,
 'calix_b6_telnet': <class 'netmiko.calix.calix_b6.CalixB6Telnet'>,
 
 # and so on...
{% endhighlight %}

[Here we see](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L219-L254) that `CLASS_MAPPER` is expanded to include `_telnet` and `_serial` classes. After having seen this, we can deduce that the `_ssh` suffix was added for consistency.  

## Finishing platforms dissection

Remember how I said that `platforms` is only 2 lines? Well, we still haven't finished dissecting the first line yet! But we're very close.

Now that we know what `CLASS_MAPPER` looks like, dissecting the [first line of `platforms`](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L256) is actually very easy. All we're doing is extracting the keys from the dictionary. Let's see how this works.

Running `CLASS_MAPPER.keys()` gives the the dictionary keys. However, the `type` of output is `dict_keys`: 

{% highlight python linenos %}
>>> CLASS_MAPPER.keys()
dict_keys(['a10', 'a10_ssh', 'accedian', 'accedian_ssh', 'alcatel_aos', 'alcatel_aos_ssh'])
>>> type(CLASS_MAPPER.keys())
<class 'dict_keys'>
{% endhighlight %}

To get the output as a standard list, we simply put it in the `list()` function:

{% highlight python linenos %}
platforms = list(CLASS_MAPPER.keys())
pprint(platforms)
['a10',
 'a10_ssh',
 'accedian',
 'accedian_ssh',
 'alcatel_aos',
 'alcatel_aos_ssh',
 'alcatel_sros',
 'alcatel_sros_ssh',
 'apresia_aeos',
 'apresia_aeos_ssh',
 'arista_eos',
 'arista_eos_ssh',
 'aruba_os',
 'aruba_os_ssh',
 'avaya_ers',
 'avaya_ers_ssh',
 'avaya_vsp',
 'avaya_vsp_ssh',
 'brocade_fastiron',
 'brocade_fastiron_ssh',
 'brocade_netiron',
 'brocade_netiron_ssh',
 'brocade_nos',
 'brocade_nos_ssh',
 'brocade_vdx',
 'brocade_vdx_ssh',
 'brocade_vyos',
 'brocade_vyos_ssh',
 'checkpoint_gaia',
 'checkpoint_gaia_ssh',
 'calix_b6',

# and so on...

 ]
>>> type(platforms)
<class 'list'>
{% endhighlight %}

Circling back to [this line](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L271) `ConnectHandler`, we can now see Netmiko knows if we've passed in a supported `device_type`. 

## Dissecting ssh_dispatcher

The [last two lines](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L276-L277) of `ConnectHandler` [involves `ssh_dispatcher`](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L280-L282).  Thanks to the work we've already done, this function is actually very easy to understand. All we're doing is taking the `device_type` which was passed in by the user, and we're returning the corresponding class.

For example, let's see what happens if we pass in a device type of `cisco_ios`:

{% highlight python linenos %}
CLASS_MAPPER['cisco_ios']
<class 'netmiko.cisco.cisco_ios.CiscoIosSSH'>
{% endhighlight %}

This class is then [saved as `ConnectionClass`](https://github.com/ktbyers/netmiko/blob/5e51b8917975c96b3320f7d0111e0b058b3cfb5d/netmiko/ssh_dispatcher.py#L276-L277). Finally, we call this class and pass it the `*args` and `**kwargs` parameters.

This leads to the question, what are valid `*args` and `**kwargs` which we can pass to Netmiko? Looking at the "Getting Started" example, we know a few of them:
* `device_type`
* `host`
* `username`
* `password`
* `port`
* `secret`

Are there others though? For example, can we use keys for authentication instead usernames and password?  

# Challenge

In this post we examined how `platforms` works. The good news is that `platforms_str`, `scp_platforms`, and `scp_platforms_str` work in much the same way. My challenge to you is to dissect them to the same level as was done in this post. Doing so is a great way to improve your Python skills.