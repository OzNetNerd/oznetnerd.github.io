---
title: Dangers of the EIGRP "Neighbor" Command
sub_heading: "Danger! Danger Will Robinson!!"
redirect_from: /dangers-eigrp-neighbor-command/

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
- Useful Commands
---
I have touched on **[EIGRP](/tags/#eigrp)** a few times before and here we are again.

In my [**EIGRP Route Advertising**](/eigrp-route-advertising/) post I explained how EIGRP neighbor relationships can be created using the **"network"** command. In this post I will explain how they can be formed using the **"neighbor"** command and the dangers of using it as well.

To do this, I'll be using the following topology:

[![topology](/assets/2015/02/topology3.jpg)](/assets/2015/02/topology3.jpg)
To get started, let's create a "normal" EIGRP network on all of the routers using the **"network"** command.

**R1:**

```
router eigrp 10
network 172.16.11.1 0.0.0.0
network 192.168.43.1 0.0.0.0
no auto-summary
```

**R2:**

```
router eigrp 10
network 172.16.11.2 0.0.0.0
no auto-summary
```

**R3:**

```
router eigrp 10
network 172.16.11.3 0.0.0.0
no auto-summary
```

**R4:**

```
router eigrp 10
network 192.168.43.2 0.0.0.0
no auto-summary
```

Once we have done that, all routers will have routes to one another. Here is an example of what **R4's** routing table looks like:

```
R4(config-router)#do sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
 D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
 N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
 E1 - OSPF external type 1, E2 - OSPF external type 2
 i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
 ia - IS-IS inter area, * - candidate default, U - per-user static route
 o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

192.168.43.0/30 is subnetted, 1 subnets
C 192.168.43.0 is directly connected, FastEthernet0/1
 172.16.0.0/29 is subnetted, 1 subnets
D 172.16.11.0 [90/307200] via 192.168.43.1, 00:07:39, FastEthernet0/1
```

One interesting thing to note, however, is the connection between **R1**, **R2** and **R3**. As they have a switch connecting the three of them together, the EIGRP multicast packets sent by each one of them reach the other two routers. This in turn allows each router to form an EIGRP adjacency to the other two routers, as per the **R2's** output below:

```
R2(config-if)#do sh ip ei ne
IP-EIGRP neighbors for process 10
H Address Interface Hold Uptime SRTT RTO Q Seq
 (sec) (ms) Cnt Num
1 172.16.11.3 Fa0/0 11 00:10:03 1573 5000 0 6
0 172.16.11.1 Fa0/0 14 00:10:40 118 1062 0 8
```

The reason why this is possible is because the switch creates a Layer 2 connection between them all. As multicast can happily travel on Layer 2 connections, the multicast packets can freely flow between the routers. However, as the connection between **R1** and **R4** is a Layer 3 connection, the multicast packets do not travel past that segment of the network, as per **R4's** output below:

```
R4(config-router)#do sh ip ei ne
IP-EIGRP neighbors for process 10
H Address Interface Hold Uptime SRTT RTO Q Seq
 (sec) (ms) Cnt Num
0 192.168.43.1 Fa0/1 12 00:19:19 129 774 0 11
```

Performing a packet capture on **R2**, we can see EIGRP multicast packets being sent from both **R1** and **R3**. These are the packets that make EIGRP adjacencies form and allow them to stay up. If these packets did not reach the other router(s), the adjacency would fail and the EIGRP neighbor relationship would be bought down.

[![capture](/assets/2015/02/capture.jpg)](/assets/2015/02/capture.jpg)

So now that we have confirmed that our "normal" EIGRP setup is up and running and all routers can communicate with one another, let's discuss the **"neighbor"** command. What the **"neighbor"** command does is it sends EIGRP messages using unicast packets as opposed to the "normal" method of using multicast packets. However, a undesirable side affect presents itself when this command is used. The interface(s) that have this command applied to them no longer send or receive EIGRP multicast packets. To better explain this, let's change the configuration on **R1** and **R2**.

**Note:** The **"neighbor"** command must be configured on the two neighboring routers. If it is only configured on one neighbor but not the other, the neighbor adjacency will not come up.

**R1:**

```router eigrp 10
 neighbor 172.16.11.2 fa0/0
```

**R2:**

```
router eigrp 10
 neighbor 172.16.11.1 fa0/0
```

Doing the above changes makes the EIGRP adjacency between the two routers drop out momentarily, as per the below logs:

```
*Mar 1 01:42:37.887: %DUAL-5-NBRCHANGE: IP-EIGRP(0) 10: Neighbor 172.16.11.3 (FastEthernet0/0) is down: Static peer configured
*Mar 1 01:42:37.991: %DUAL-5-NBRCHANGE: IP-EIGRP(0) 10: Neighbor 172.16.11.1 (FastEthernet0/0) is up: new adjacency
```

As you can see from the second line, the adjacency comes back up. We can confirm this using the **"show ip eigrp neighbors"** command:

**R2:**

```
R2#sh ip ei ne
IP-EIGRP neighbors for process 10
H Address Interface Hold Uptime SRTT RTO Q Seq
 (sec) (ms) Cnt Num
0 172.16.11.1 Fa0/0 12 00:00:07 88 528 0 14
```

However, if we take a look at **R3's** logs and **"show ip eigrp neighbors"** output, we see a very different story - there are no adjacencies at all:

```
 *Mar 1 01:42:37.323: %DUAL-5-NBRCHANGE: IP-EIGRP(0) 10: Neighbor 172.16.11.2 (FastEthernet0/0) is down: Interface Goodbye received

R3#sh ip ei ne
IP-EIGRP neighbors for process 10
```

As mentioned above, this is because the **"neighbor"** command stops that particular interface from sending and receiving EIGRP multicast messages. However, as this command is interface specific, only interface **Fa0/0** on **R1** and **R2** are affected. This is why **R1's fa0/1** interface is still able to maintain an EIGRP adjacency to **R4**:

**R4:**

```
R4(config-router)#do sh ip ei ne
IP-EIGRP neighbors for process 10
H Address Interface Hold Uptime SRTT RTO Q Seq
 (sec) (ms) Cnt Num
0 192.168.43.1 Fa0/1 13 03:26:18 129 774 0 11
```

If we perform a packet capture on **R3** now, we can see that it is the only device sending multicast packets, as opposed to the packet capture screenshot above where we saw all devices sending multicast packets:

[![capture2](/assets/2015/02/capture2.jpg)](/assets/2015/02/capture2.jpg)
However, if we perform a packet capture on **R2**, we can see that it is not only receiving **R3's** multicast packets, but it is also sending and receiving unicast packets to and from **R1**:

[![capture3](/assets/2015/02/capture3.jpg)](/assets/2015/02/capture3.jpg)
In conclusion what you need to remember is that an interface cannot send and receive both unicast and multicast EIGRP messages, it is one or the other. It is important to remember this as it can be easily overlooked when troubleshooting an issue.

**Update:** For those who would like to read more about the **"neighbor"** command, see [**this blog**](http://rbcciequest.wordpress.com/2008/02/04/day-6-of-week-4-and-the-end-of-week-4/) which I stumbled across after writing this post.
