---
title: Single Public IP NATing to Multiple Hosts
sub_heading: "One-to-Many NAT'ing"
redirect_from: /single-public-ip-nating-multiple-hosts/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- NAT

tags:
- GNS3
- Labs
- Cisco
- NAT
---
In my previous blog entry, [**Multiple Public IP NATing to Multiple Hosts**](/multiple-public-ip-nating-multiple-hosts/ "Multiple Public IP NATing to Multiple Hosts"), I described how you can use "one to one" NATing to allocate one public IP address per internal host. This is a great solution for those who have multiple public IPs, however, these usually come at an added monthly cost.

An alternative to this method would be to run your services on different port numbers. For example, if you have three web servers, you could run one on port 80, the next on port 8080 and the last one on port 8081. However, some System Administrators do not like forcing their applications to run on non-standard ports and some applications may not even offer you the option.

So how do we fix this issue you ask? Rather than do the port change on the application(s) themselves, you can simply do it on the router itself. This is a great solution because the applications themselves do not need to be changed and you've got a central point of configuration for all of the port changes. Also, should you decide to invest in additional IP addresses later on down the track, you can migrate the server(s) to their new IP address(es) by simply changing a few lines of configuration.

Using the diagram below, I'll describe how you can achieve this:

[![topology](/assets/2015/02/topology6.jpg)](/assets/2015/02/topology6.jpg)

Using the **"ip nat inside source static [tcp|udp]"** command, we can map port **23** (telnet) for each of the LAN hosts (**192.168.45.2**, **192.168.45.3** and **192.168.45.4**) to different ports (23, 2300 and 2301 respectively).

```
R1(config)#do sh run | inc ip nat inside source static
ip nat inside source static tcp 192.168.45.2 23 94.56.43.2 23 extendable
ip nat inside source static tcp 192.168.45.3 23 94.56.43.2 2300 extendable
ip nat inside source static tcp 192.168.45.4 23 94.56.43.2 2301 extendable
```

What this means is that if we try to telnet to:

*   94.56.43.2 on port 23, we'll connect to 192.168.45.2
*   94.56.43.2 on port 2300, we'll connect to 192.168.45.3
*   94.56.43.2 on port 2301, we'll connect to 192.168.45.4

As mentioned above, the beauty of configuring the port changes on the internet router itself (**R1**), we don't need to force telnet to run on different ports on the other hosts LAN hosts (**R2**, **R3** and **R4**).

Let's look at some examples. Here is **R1's** NAT table before any traffic has been sent:

```
R1(config)#do sh ip nat trans
Pro Inside global      Inside local       Outside local     Outside global
tcp 94.56.43.2:23      192.168.45.2:23    ---                ---
tcp 94.56.43.2:2300    192.168.45.3:23    ---                ---
tcp 94.56.43.2:2301    192.168.45.4:23    ---                ---

Now I'll initiate some telnet sessions from **R1** to **94.56.43.2** and it's multiple ports. I'll start with port 23:

R5#telnet 94.56.43.2 23
Trying 94.56.43.2 ... Open
```

Now let's take a look at the NAT translation table:

```
R1(config)#do sh ip nat trans
Pro Inside global      Inside local       Outside local      Outside global
tcp 94.56.43.2:23      192.168.45.2:23    203.43.94.1:42931  203.43.94.1:42931
tcp 94.56.43.2:23      192.168.45.2:23    ---                ---
tcp 94.56.43.2:2300    192.168.45.3:23    ---                ---
tcp 94.56.43.2:2301    192.168.45.4:23    ---                ---
```

Now I'll try ports 2300 and 2301:

 ```
R5#telnet 94.56.43.2 2300
Trying 94.56.43.2, 2300 ... Open
R5#
R5#telnet 94.56.43.2 2301
Trying 94.56.43.2, 2301 ... Open
```

And now let's take another look at the NAT translation table:

```
R1(config)#do sh ip nat trans
Pro Inside global      Inside local       Outside local      Outside global
tcp 94.56.43.2:23      192.168.45.2:23    ---                ---
tcp 94.56.43.2:2300    192.168.45.3:23    203.43.94.1:23292  203.43.94.1:23292
tcp 94.56.43.2:2300    192.168.45.3:23    ---                ---
tcp 94.56.43.2:2301    192.168.45.4:23    203.43.94.1:49225  203.43.94.1:49225
tcp 94.56.43.2:2301    192.168.45.4:23    ---                ---
```