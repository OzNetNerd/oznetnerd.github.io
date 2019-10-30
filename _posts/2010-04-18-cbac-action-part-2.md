---
title: CBAC in Action, Part 2
sub_heading: "Diving deeper with CBAC"
redirect_from: /cbac-action-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Security

tags:
- NBAR
- Security
- Labs
- Cisco
---
In my [**previous CBAC post**](/cbac-action-part-1/) I covered how to deny all external traffic unless it is in response to a request someone on the LAN has made, e.g If you send a ping, CBAC will allow the ping reply traffic to come through the firewall.

However, this situation may not be ideal for everyone. What if you wanted to allow one or more protocols in to your network but still have the security that CBAC provides? For example, if you wanted a contractor to be able to SSH in to your network any time, day or night? I will show you how using the same topology as last time (except you will see that **R3** has been renamed to SSH Source). Our goal is to allow **R3** to SSH in to **R2** without being blocked, while still blocking **R4** (hacker) completely.

[![topology](/assets/2015/02/topology.jpg)](/assets/2015/02/topology.jpg)

If you recall, our inbound ACL on R1 looks like this:

```
ip access-list extended DENY_IN
deny ip any any
```

So if we try to SSH in from **R3** now, we should fail, which we do. See the output below:

```
R3#ssh -l cisco 192.168.0.2
% Destination unreachable; gateway or host down
```

However, if we add the following line to the ACL:

```
ip access-list extended DENY_IN
!!Allow SSH traffic in to the network if its source address is 10.10.10.2 and it is destined for 192.168.0.2
permit tcp host 10.10.10.2 host 192.168.0.2 eq 22
deny ip any any
```

We should now be able to SSH in to **R2** from **R3**, which we can:

```
R3#ssh -l cisco 192.168.0.2

Password:
R2>
```

But just to make sure that all of our security measures are still enforced, lets try to ping from **R3** to **R2**:

```
R3#ping 192.168.0.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.2, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
R3#
```

And let's also try to SSH in from **R4** (hacker):

```
R4#ssh -l cisco 192.168.0.2
% Destination unreachable; gateway or host down

R4#
```

As you can see from the above two tests, even though **R3's** SSH traffic can get through to **R2**, its ping traffic cannot. And you can also see that **R4'**s SSH traffic cannot get through at all. This is because the new ACL entry specifically says that only SSH traffic sourced from **R3** and destined for **R2** is allowed to enter the network.