---
title: EIGRP No Auto Summary Command, Part 2
sub_heading: "A deeper look at the 'no auto-summary' command"
redirect_from: /eigrp-no-auto-summary-command-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- EIGRP
- Subnetting

tags:
- GNS3
- Labs
- Cisco
- EIGRP
- Routing Protocols
- Auto Summary
- Summary Addressing
- Subnetting
---
A few years ago I wrote a blog post about EIGRP and the **"auto-summary"** command called [**EIGRP No Auto Summary Command, Part 1**](/eigrp-no-auto-summary-command-part-1/ "EIGRP No Auto Summary Command, Part 1"). In that post I provided a brief description of what **"auto-summary"** does and demonstrated how it works by creating a basic lab. Now that you've seen the basics though, it is time to dig a little deeper.

In the previous post we saw that **R1** and **R2** were both automatically summarising the **10.45.100.0/24** and **10.16.0.0/24** networks respectively and therefore **R3** and **R4** could not reach one another. That's seems normal enough seeing as though the command is called "auto-summary". However, things aren't that simple unfortunately.

For example, take a look at this topology:

[![topology](/assets/2015/02/topology2.png)](/assets/2015/02/topology2.png)

Both routers are running EIGRP, but only **R1** has the **"auto-summary"** command enabled, as per the configurations below:

**R1:**

```
R1(config-router)#do sh run | s router ei
router eigrp 1
network 10.1.1.1 0.0.0.0
network 10.2.2.2 0.0.0.0
network 10.10.10.2 0.0.0.0
network 172.16.1.1 0.0.0.0
**auto-summary**
```

**R2:**

```
R2(config-router)#do sh run | s router ei
router eigrp 1
network 10.10.10.1 0.0.0.0
**no auto-summary**
```

Due to the **"auto-summary"** command being used it would be easy to assume that **R1** would advertise two summary addresses - **10.0.0.0/8** and **172.16.0.0/16** to **R2 **instead of its **/24** routes. However, this is not the case:

**R2:**

```
R2(config-router)#do sh ip route | b Gate
Gateway of last resort is not set
**D    172.16.0.0/16 [90/409600] via 10.10.10.2, 00:17:22, FastEthernet0/0
10.0.0.0/24 is subnetted, 3 subnets**
C       10.10.10.0 is directly connected, FastEthernet0/0
D       10.2.2.0 [90/409600] via 10.10.10.2, 00:17:22, FastEthernet0/0
D       10.1.1.0 [90/409600] via 10.10.10.2, 00:17:22, FastEthernet0/0
```

Above we see two different things:

*   In **blod**, we see the **172.16.0.0/16** summary route which we were expecting given that **R1** is using the **"auto-summary"** command.
*   In _italics_, we see the individual **10.x.x.x/24** addresses being advertised from **R1** instead of the **/8** summary route we were expecting.

So this leaves us with the question - why is it that the **172.16.1.0/24** network was successfully summarised but the **10.x.x.x/24** networks weren't? The answer is that when **"auto-summary"** is enabled, routes are only summarised when both of the following requirements are met:

1.  The router is directly connected to **two differing IP address classes**.
2.  An advertisement must go from **one class to the other**.

_Further to this, only these directly connected networks' addresses will be summarised. All non-directly connected networks which are advertised to this router ARE NOT summarised (I will provide more information on this in a future blog post)._

Let me expand on the two requirements (forget the part in red for now as it may be too confusing to try to comprehend it all in one hit) - In the above diagram we can see that **R1** has addresses which belong to two classful boundaries:

**10.0.0.0/8 **(Class A) which consists of the following networks -

*   10.1.1.0 /24
*   102.2.0 /24
*   10.10.10.0 /24

**172.16.0.0/16 **(Class B) which consists of the following networks -

*   172.16.1.0 /24

Given that **R1** has direct connections to Class A and Class B addresses, this means that the first requirement has been met.

Next, we see that **R1** and **R2** have formed an adjacency over their Class A addresses. This means that the 10.x.x.x /24 networks **do not** conform to the second requirement, however, it also means that the  **172.16.1.0** network **does**. This is because the **172.16.1.0** network (Class B) is being advertised to a neighbour who is on a Class A network.