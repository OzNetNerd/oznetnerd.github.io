---
title: EIGRP Route Advertising
sub_heading: "Learning to advertise EIGRP routes"
redirect_from: /eigrp-route-advertising/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- EIGRP

tags:
- GNS3
- Labs
- Cisco
- EIGRP
- Routing Protocols
---
In the forums that I frequently visit I always see people asking how EIGRP route advertising works. The users are not sure how to correctly use the **"network"** command and usually use more commands than are necessary. So in today's post, using the diagram below, I will explain how to advertise EIGRP routes properly.

Now before we get started let me explain what the **"network"** command actually does. The **"network"** command tells the router which directly connected routes (routes which the router owns/is a part of) it can advertise to its peers. In order for two routers to exchange EIGRP routes, they must both be part of the same subnet and have a **"network"** command specifying that particular subnet, or, at the very least, their specific IP address on that subnet. Confused? That's OK, I'll explain it in more detail soon.

Here is the topology we'll be using:

[![topology](/assets/2015/02/topology1.jpg)](/assets/2015/02/topology1.jpg)  
Let's take a look at what **R1's** routing table looks like:

```
10.0.0.0/32 is subnetted, 1 subnets
C 10.16.1.1 is directly connected, Loopback0
C 192.168.1.0/24 is directly connected, FastEthernet0/0
```

As you can see, **R1** only has routes to its directly connected networks. At this stage **R1** has no idea that the **10.16.2.1** IP address on **R2** even exists because **R2** has no way of telling him. To rememdy this, lets enable EIGRP:

**R1:**

```
router eigrp 10
network 192.168.1.1 0.0.0.0
no auto-summary
```

**R2:**

```
router eigrp 10
network 192.168.1.2 0.0.0.0
no auto-summary
```

**Note:** Please see my [**no auto-summary**](/eigrp-no-auto-summary-command-part-1/ "EIGRP No Auto Summary Command, Part 1") post for more information on what this command does.

The reason why we have used the **192.168.1.1** and **192.168.1.2** addresses with the **"network"** command is because, as per the above, EIGRP adjacencies can only be created between routers on the same subnet. By adding these commands, we have now started an EIGRP relationship, as the below log message tells us:

```
*Mar 1 00:32:02.935: %DUAL-5-NBRCHANGE: IP-EIGRP(0) 10: Neighbor 192.168.1.2 (FastEthernet0/0) is up: new adjacency
```

OK so now that we've got an EIGRP adjacency, let's take another look **R1's** routing table:

```
10.0.0.0/32 is subnetted, 1 subnets
C 10.16.1.1 is directly connected, Loopback0
C 192.168.1.0/24 is directly connected, FastEthernet0/0
```

As you can see, nothing has changed routing table wise. This is because we have not added network statements to the EIGRP configuration that allows the routers to advertise their other networks. Let's do that now:

**R1:**

```
router eigrp 10
network 10.16.1.1 0.0.0.0
no auto-summary
```

**R2:**

```
router eigrp 10
network 10.16.2.1 0.0.0.0
no auto-summary
```

Now that we have done that, let's take another look at R1's routing table:

```
10.0.0.0/32 is subnetted, 2 subnets
D 10.16.2.1 [90/409600] via 192.168.1.2, 00:00:04,FastEthernet0/0
C 10.16.1.1 is directly connected, Loopback0
C 192.168.1.0/24 is directly connected, FastEthernet0/0
```

As per the second line in the output above, **R1** can now see **R2's** other network. This is because **R2** told **R1** about it through an EIGRP update.