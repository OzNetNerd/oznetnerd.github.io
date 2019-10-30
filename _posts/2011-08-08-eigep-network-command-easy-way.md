---
title: EIGRP Network Command - The Easy Way Out
sub_heading: "Advertising EIGRP routes isn't as hard as it seems"
redirect_from: /eigep-network-command-easy-way/

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

In my **[EIGRP Route Advertising](/eigrp-route-advertising/ "EIGRP Route Advertising")** post I said _"in order for two routers to exchange EIGRP routes, they must both be part of the same subnet and have a network command specifying that particular subnet, or, at the very least, their specific IP address on that subnet"_. In that entry I then went on to use the routers' specific IP addresses. In this entry, I'm going to explain how you can specify a larger subnet in order to reduce the number of network commands you must apply.

To do this, I'll use the following topology:

[![topology](/assets/2015/02/topology4.jpg)](/assets/2015/02/topology4.jpg)

We'll configure the **R2** and **R3** in the same way as we did previous, by specifying the exact addresses of the interfaces we want to advertise.

**R2:**

```
R2(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 10.34.19.1 0.0.0.0
 network 192.168.20.2 0.0.0.0
 no auto-summary
```

**R3:**

```
R3(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 10.34.19.2 0.0.0.0
 network 192.168.10.2 0.0.0.0
 no auto-summary
```

**Note:** Please see my [**no auto-summary**](/eigrp-no-auto-summary-command-part-1/ "EIGRP No Auto Summary Command, Part 1") post for more information on what this command does.

In regards to the above two configurations, we have used one **"network"** command per interface, however, with **R1**, as its two interfaces have the same two octets, you can use one **"network"** command that covers both interfaces. Here's how:

**R1:**

```
R1(config-router)#do sh run | begin router eigrp
router eigrp 10
 network 192.168.0.0 0.0.255.255
 no auto-summary
```

Doing this has the exact same affect as using two separate network commands, as you can see by **R1's** neighbor table:

**R1:**

```
R1(config-router)#do sh ip ei ne
IP-EIGRP neighbors for process 10
H Address Interface Hold Uptime SRTT RTO Q Seq
 (sec) (ms) Cnt Num
1 192.168.10.2 Fa0/0 11 00:12:26 169 1014 0 13
0 192.168.20.2 Fa0/1 11 00:17:14 1311 5000 0 8
```

Please note though that it is best practice to be as specific as possible when dealing with things like route advertisements. Using a blanket **"network"** statement like the one used in this example is not always a good idea, especially when dealing with large, complex networks.