---
title: A Deep Dive in OSPF LSAs, Part 4
sub_heading: "A series of posts to help you become an OSPF expert!"
redirect_from: /a-deep-dive-in-ospf-lsas-part-4/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- OSPF

tags:
- Cisco
- OSPF
- LSAs
- GNS3
- Labs
---
As mentioned a couple of times in this series, Type 1 LSAs differ between Broadcast and Point-to-Point segments. To demonstrate this, I'll be using the same topology as before. The only difference is that each interface has had the "ip ospf network point-to-point" command applied to it.

[![diag2](/assets/2015/04/diag2.png)](/assets/2015/04/diag2.png)

You may recall that the reason why I'm talking only about Type 1 LSAs is because Type 2 LSAs are only present in broadcast segments. In case you need it, here's a quick refresher:

*   **Type 1 – Router LSA** – each router announces its presence and lists the links to other routers or networks in the same area, together with the metrics to them. Type 1 LSAs are flooded across their own area only. The link-state ID of the type 1 LSA is the originating router ID.
*   **Type 2 – Network LSA** – the designated router (DR) on a broadcast segment (e.g. Ethernet) lists which routers are joined together by the segment. Type 2 LSAs are flooded across their own area only. The link-state ID of the type 2 LSA is the IP interface address of the DR.

## Point-to-Point Networks – Type 1 LSAs

To begin with, let's first take a look at the area's database:

```
R1(config-router)#do sh ip os da    

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         780         0x80000002 0x00B133 2
2.2.2.2         2.2.2.2         671         0x80000005 0x006519 4
3.3.3.3         3.3.3.3         664         0x80000003 0x007558 2
```

Here we see two differences as compared to the output we saw in the previous "Broadcast" example. (This output is found in the "**Topology #2**" section of **[Part 2](/a-deep-dive-in-ospf-lsas-part-2/ "A Deep Dive in OSPF LSAs, Part 2")**).

1.  There are no Type 2 LSAs.
2.  The Link count has doubled even though the topology is exactly the same.

As we already knew not to expect any Type 2 LSAs, let's find out why the link count has doubled.

```
R1(config-router)#do sh ip os da ro

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

  LS age: 11
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 1.1.1.1
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000003
  Checksum: 0xAF34
  Length: 48
  Number of Links: 2

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 2.2.2.2
     (Link Data) Router Interface address: 10.1.2.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.2.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 1812
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 2.2.2.2
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000005
  Checksum: 0x6519
  Length: 72
  Number of Links: 4

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 3.3.3.3
     (Link Data) Router Interface address: 10.2.3.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.2.3.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 1.1.1.1
     (Link Data) Router Interface address: 10.1.2.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.2.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 1806
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 3.3.3.3
  Advertising Router: 3.3.3.3
  LS Seq Number: 80000003
  Checksum: 0x7558
  Length: 48
  Number of Links: 2

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 2.2.2.2
     (Link Data) Router Interface address: 10.2.3.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.2.3.0
     (Link Data) Network Mask: 255.255.255.0
      Number of TOS metrics: 0
       TOS 0 Metrics: 10
```

In the above output we see that the routers are advertising the networks they have links on (e.g R1's 10.1.2.0 advertisement), which is similar to what we saw in the "Broadcast" example mentioned above. However, we also see that the routers are sending an additional link advertisement which contains the RID of it's neighbour (e.g R1's 2.2.2.2 advertisement).

To make sure we get the most out of these LSAs, let's break them down further:

*   As the "Advertising Router" is 1.1.1.1, we know that R1 sent this advertisement.
*   R1 knows that this link is a point-to-point connection and therefore advertises the neighbours RID as so.
    *   R1 also knows that it has to use its 10.1.2.1 interface to reach its neighbour's RID 2.2.2.2 (R2).
*   R1's second link advertisement is referred to as a stub. Believe it or not, this has absolutely nothing to do with a stub area, totally stubby area, NSSA, etc. It is just the way OSPF labels point-to-point links.
    *   This part of the advertisement describes the link's subnet and subnet mask.

## Point-to-Point Vs Broadcast LSAs

To conclude this blog post I put the image below together. My aim is to provide a reference which quickly shows the differences between R1's Type 1 Point-to-Point LSAs (seen in this blog post) and R1's Type 1 and Type 2 Broadcast LSAs (seen in [**part 2**](/a-deep-dive-in-ospf-lsas-part-2/ "A Deep Dive in OSPF LSAs, Part 2")).

[![lsas-compared](/assets/2015/04/lsas-compared.png)](/assets/2015/04/lsas-compared.png)

## Other Posts in this Series

See the [**A Deep Dive in OSPF LSAs - Index**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") post for links to all of the posts in this series.