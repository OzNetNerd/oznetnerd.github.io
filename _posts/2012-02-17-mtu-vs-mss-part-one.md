---
title: MTU Vs MSS - Part One
sub_heading: "Maximum Transmission Unit Versus Maximum Segment Size"
redirect_from: /mtu-vs-mss-part-one/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Misc.

tags:
- Cisco
- Useful Commands
- MSS
- MTU
---
Have you ever seen the below configuration and wondered what these commands do? And why the MSS value always seems to be 40 bytes lower than the MTU?

```
interface Dialer1
 ip mtu 1440
 ip tcp adjust-mss 1400
```

Well, over the course of my next couple of blog entries, I plan to tell you all about them.

From my countless number of Google searches, the best information I could find was:

*   TCP MSS operates at Layer 4. It is 40 bytes lower than the IP MTU as it does not take headers in to consideration (20 byte IP and 20 byte TCP).
*   IP MTU operates at Layer 3. It is the maximum size a packet can be before it needs to be fragmented (or dropped if the df-bit is set).
*   Ethernet MTU (Layer 2) - 1500 bytes, excluding the header and trailer.

This is good information, but it doesn't tell you why you need to set both the MSS **and** the MTU.

Using Wireshark, and speaking with the helpful people over at Whirlpool, I was able to get a deeper understanding of how these two come together.

As the syntax of the command suggests, the **"ip tcp adjust-mss"** command only works for TCP connections. When applied to a router's interface, the router will monitor this interface's **incoming and outgoing traffic**, looking for SYN packets (which is where hosts define their MSS). When it sees a SYN packet, it will automatically change the MSS field to the size that you specified using the command above. When the receiving host receives the SYN packet, it will see the new MSS that was set by the router, and will reply with an MSS equal to or less than the one specified by the initiating host.

(Just to reiterate, as the router inspects both incoming and outgoing traffic, it doesn't matter whether the connection is initiated by a local host or a remote one, the SYN packet will still be altered).

**Note:** If the **"ip tcp adjust-mss"** command is also set on the remote end of the connection, the lower MSS will be used.

To put it simply, the **"ip tcp adjust-mss"** command is used to prevent "blackhole routing".

By using this command, you can ensure that your packets are not being fragmented in transit. Fragmentation is far from ideal as it can lead to things like delayed packet delivery and packet drops.

Speaking of fragmentation, this is where the IP MTU comes in to play. When applied to an interface, the IP MTU basically states that packets **leaving** this interface need to be equal to, or less than the specified size. If they are larger, they will be fragmented. This is why the **"ip tcp adjust-mss"** command is necessary. By setting the MSS to 40 bytes below the MTU, you ensure that all of the packets sent from your network will not be fragmented by your routers.

Unfortunately though, setting the IP MTU and the MSS **does not** guarantee your packets won't be fragmented or dropped on their way to their destination. This is because each router your data passes on its way to the destination will be configured differently, and may have different MTU sizes on their interfaces.

If you find your having connectivity issues, there are tools you can use like mturoute.exe that will tell you the MTU of each hop between you and the destination network.

Finally, the Ethernet MTU. This is the MTU that your Layer 2 traffic uses. Setting this to a higher value (e.g 9000 bytes for Jumbo Frames) allows you to send more data per frame, meaning that you've got less overhead per packet. This enables you to deliver much larger amounts of data in shorter periods of time.

And that's it for this entry :) In my next blog post I'll delve deeper in to MSS and include some packet captures to demonstrate how it works.

**Update:** [**MTU Vs MSS - Part Two**](/mtu-vs-mss-part-two/ "MTU Vs MSS – Part Two") has now been published.