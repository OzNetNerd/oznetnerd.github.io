---
title: EIGRP No Auto Summary Command, Part 1
sub_heading: "Why you should disable EIGRP auto-summary"
redirect_from: /eigrp-no-auto-summary-command-part-1/

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
- Auto Summary
- Summary Addressing
- Useful Commands
---
In my previous post about [**EIGRP Route Advertisements**](/eigrp-route-advertising/) I touched on the **"no auto-summary"** command, but did not delve in to the details of what this command actually does, so today I will.

In a nutshell (see [**Part 2**](/eigrp-no-auto-summary-command-part-2/ "EIGRP No Auto Summary Command, Part 2") for a more detailed explanation), when using EIGRP the **"auto-summary"** command (which is enabled by default), what happens is that the router automatically creates summary routes for all of the networks which you specify in your EIGRP configuration (with the **"network"** command). And these aren't good summary routes either - they are routes which are summarised to their classful boundary.

For example, if you issue the following command:

```
network 10.45.100.0 0.0.0.255
```

Instead of advertising the network as **10.45.100.0 /24**, the router will advertise the entire **10.0.0.0 /8** network instead. To prevent this from happening (which just about everyone does these days), you must issue the following command:

```
no auto-summary
```

To see what type of "damage" can be caused by leaving the default **"auto-summary"** command enabled, please read on.

Here is the topology I'll be using to demonstrate:

[![topology](/assets/2015/02/topology2.jpg)](/assets/2015/02/topology2.jpg)

As you can see this is a pretty straight forward network. Here is a brief summary of how it is configured:

*   All routers are running EIGRP (AS10)
*   There are EIGRP neighbor adjacencies between each point-to-point link (**R4** to **R1**, **R1** to **R2**, **R2** to **R3**) - This means all routers will share their routing table information with one another
*   All routers have the **"auto-summary"** command applied to their EIGRP AS10 configuration

Now because all of the routers are sharing their routes with one another you would expect to be able to ping from **R4** all the way through to **R3**, wouldn't you? Well, unfortunately that is not the case at the moment:

```
R4#ping 10.16.0.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.16.0.2, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

To troubleshoot this issue, let's take a look at the routing table on **R4**:

**R4:**

```
10.0.0.0/24 is subnetted, 1 subnets
C 10.45.100.0 is directly connected, FastEthernet0/1
D 192.168.0.0/24 [90/307200] via 10.45.100.1, 00:17:31, FastEthernet0/1
```

As you can see, **R4** knows how to get to the 10.45.100.0 network (because it is directly connected) and the **192.168.0.0 /24** network but it has no idea how to get to the **10.16.0.0/24** network. To find out why, let's take a look at the next hop router, **R1**. Here is the output of **R1's** "show ip route" command:

**R1:**

```
10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D 10.0.0.0/8 is a summary, 00:03:15, Null0
C 10.45.100.0/24 is directly connected, FastEthernet0/1
C 192.168.0.0/24 is directly connected, FastEthernet0/0
```

As per the second line in the output above, it looks like we may have found part of the issue. The reason why **R4** cannot get to **R3** could be because **R1** is advertising a summary route for the entire **10.0.0.0 /8** range. Why is it doing this you ask? Because **R1** is automatically summarising the **10.45.100.0** network to its classful boundary and then passing the advertisement on to **R2**. What makes matters worse is the fact that when summary routes are used, the router which creates the summary points the network to its **Null0** interface. In other words, if **R1** does not have a more specific to **10.0.0.0** networks, it will drop the traffic. This explains why when **R4** tries to reach **R3**, it can't.

However, **R1** is not entirely to blame. This is because its summary route, if it were the only summary route in the network, would in fact allow the pings to work. It wouldn't be an ideal setup, but it would work.

Unfortunately though, it is not the only summary route in the network. If we take a look at **R2'**s routing table, we will see that it too has a summary route for the exact same range:

**R2:**

```
10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D 10.0.0.0/8 is a summary, 00:07:18, Null0
C 10.16.0.0/24 is directly connected, FastEthernet0/1
C 192.168.0.0/24 is directly connected, FastEthernet0/0
```

So what does this mean you ask? The problem is that both **R1** and **R2** believe that they can get to the entire **10.0.0.0 /8** range themselves and therefore they don't need each other. However, this is incorrect because **R1** has access to the **10.45.100.0 /24** subnet and **R2** has access to the **10.16.0.0 /24** subnet, so they do in fact need each other. Unfortunately though, as their summary routes point to **Null0**, when they are asked to reach a **10** network they don't have a more specific route for, they drop the traffic.

Looking at **R1** and **R2's** routing tables above, we can see that they have more specific routes to the **10.45.100.0 /24** and **10.16.0.0 /24** networks respectively. This means that **R1** won't drop traffic destined for **10.45.100.0 /24** and **R2** won't drop traffic destined for **10.16.0.0 /24**, however, they will both drop traffic destined for all other **10.0.0.0** networks as they don't have any more specific routing table entries to use.

To remedy this situation, one of the routers is going to have to give up their summary route (ideally though, both of them would). By stopping one of the routers from advertising the summary route, both routers will end up advertising different routes as opposed to identical summary routes.

Let's make the change on **R1**:

```
router eigrp 10
no auto-summary
```

Now let's take a look at R1's routing table - (Compare it to R1's routing table output above to see the difference):

**R1:**

```
10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D 10.0.0.0/8 [90/307200] via 192.168.0.2, 00:00:07, FastEthernet0/0
C 10.45.100.0/24 is directly connected, FastEthernet0/1
C 192.168.0.0/24 is directly connected, FastEthernet0/0
```

As per the output above, **R1** no longer believes that it has a summary route to the **10.0.0.0 /8** range of addresses and therefore has removed its route which was pointing to the **Null0** interface. And, now that that's gone, **R1** has now allowed itself to accept the summary route **R2** is advertising, as per the second line above.

Now let's take a look at **R2's** routing table to see what has changed there - (Compare it to **R2's** routing table output above to see the difference):

```
10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
D 10.0.0.0/8 is a summary, 00:06:58, Null0
C 10.16.0.0/24 is directly connected, FastEthernet0/1
D 10.45.100.0/24 [90/307200] via 192.168.0.1, 00:06:58, FastEthernet0/0
C 192.168.0.0/24 is directly connected, FastEthernet0/0
```

As per the second line in the output above, as you would expect, **R2** still has its summary route in its routing table. However, if you take a look at the fourth line you will see that **R2** now has a route to the **10.45.100.0 /24** network through **R1** which it did not have before. Because of this, **R2** now has a more specific route to the **10.45.100.0 /24** network than the one offered by the summary route. This means that instead of forwarding traffic destined for the **10.45.100.0 /24** network to **Null0** as it did before, **R2** will now forward it to **192.168.0.1**, which is **R1**.

So, now that everyone is playing nicely, let's take a look at **R4's** routing table to see if it has changed:

```
10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D 10.0.0.0/8 [90/332800] via 10.45.100.1, 00:40:02, FastEthernet0/1
C 10.45.100.0/24 is directly connected, FastEthernet0/1
D 192.168.0.0/24 [90/307200] via 10.45.100.1, 01:13:37, FastEthernet0/1
```

As per the second line in the output above, **R4** has now installed the summary route advertised by **R2**, and, because **R2** has a route to **R3** this should mean that we are able to ping the whole way through - which is what we set out to do originally. Let's give it a try:

```
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.16.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 16/66/168 ms
```

As expected, our network is now running AOK.

In conclusion I would like to repeat that you should always use the **"no auto-summary"** command, and therefore always do your summarisiations manually.

### Update

Please see [**Part 2**](/eigrp-no-auto-summary-command-part-2/ "EIGRP No Auto Summary Command, Part 2") for more information on the **"auto-summary"** command.