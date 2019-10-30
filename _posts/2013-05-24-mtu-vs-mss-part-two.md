---
title: MTU Vs MSS - Part Two
sub_heading: "Diving deeper on MTU & MSS"
redirect_from: /mtu-vs-mss-part-two/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Misc.

tags:
- GNS3
- Labs
- Cisco
- Useful Commands
- MSS
- MTU
---
A little while back I posted an entry called [**MTU Vs MSS - Part One**](/mtu-vs-mss-part-one/ "MTU Vs MSS – Part One"). At the time the plan was to follow it up with Part Two a short time later, however, here it comes over a year late :) I do apologise for that.

What prompted me to get back to writing Part Two was an e-mail from a reader who asked how I came to the conclusion that using the **"ip tcp adjust-mss"** command affects a SYN packet's MSS regardless of whether it is applied to the inbound or outbound interface. The reader also asked if I have any links to documentation that describes this. The way in which I came to the conclusion was by labbing it up. Unfortunately though I do not have any documentation that backs me up.

This blog post will demonstrate the lab I used to come to the above conclusion.

Here is the topology that I used. I have marked each link with a letter to mark the points at which the proceeding packet captures were done. (Note that PC1 and PC2 are GNS3 routers with their icons changed).

## [![topology](/assets/2015/02/topology1.png)](/assets/2015/02/topology1.png)

## No MSS Change

Before making any changes to the MSS, I'll first initiate a telnet session from PC1 to PC2.

**Packet Capture from PC1's F0/0 interface ("A"):**

[![capture1](/assets/2015/02/capture1.png)](/assets/2015/02/capture1.png)

**Packet Capture from R2's F0/1 interface ("B"):**

[![capture2](/assets/2015/02/capture2.png)](/assets/2015/02/capture2.png)

Looking at the above captures we can see that the **PC1** sent an MSS of **536** in its SYN packet, and **PC2** responded with an MSS of **536** in its SYN/ACK packet.

## R2 F0/0 MSS Change

Now that we've done our benchmark test, let's see what happens when we set an MSS of **500** on **R2's F0/0** interface.

```
R2(config)#int f0/0
R2(config-if)#ip tcp adjust-mss 500
```

Now let's repeat the telnet test:

**Packet Capture from PC1's F0/0 interface ("A"):**

[![capture3](/assets/2015/02/capture3.png)](/assets/2015/02/capture3.png)

**Packet Capture from R2's F0/1 interface ("B"):**

[![capture4](/assets/2015/02/capture4.png)](/assets/2015/02/capture4.png)

Here we can see that when the packet leaves **PC1**, it is sending its SYN packet with an MSS of **536**, but when the packet leaves **R2**, the MSS has changed to **500**.

## R2 F0/1 MSS Change

Now I'll remove the **"ip tcp adjust-mss"** command from **R2's F0/0** interface and will apply it to **R2's F0/1**, this time using an MSS of **510**.

```
R2(config)#int f0/1
R2(config-if)#ip tcp adjust-mss 510
```

Now let's repeat the telnet test:

**Packet Capture from PC1's F0/0 interface ("A"):**

[![capture5](/assets/2015/02/capture5.png)](/assets/2015/02/capture5.png)

**Packet Capture from R2's F0/1 interface ("B"):**

[![capture6](/assets/2015/02/capture6.png)](/assets/2015/02/capture6.png)

Here we can see the results are the same as the ones seen when the **"ip tcp adjust-mss"** command was put on **R2's F0/0** interface.

## MSS on Both Interfaces

As you might expect, if you set the  **"ip tcp adjust-mss"** command on both of the router's interfaces, it will use the lower of the two.