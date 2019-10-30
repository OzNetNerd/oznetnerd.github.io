---
title: A Deep Dive in OSPF LSAs, Part 5
sub_heading: "A series of posts to help you become an OSPF expert!"
redirect_from: /a-deep-dive-in-ospf-lsas-part-5/

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
In [**Part 3**](/a-deep-dive-in-ospf-lsas-part-3/ "A Deep Dive in OSPF LSAs, Part 3") I demonstrated how you can use Type 1 and Type 2 LSAs to map the topology an OSPF area. In this post we'll do the reverse. By looking at a network diagram below, you should be able to determine the following:

1.  Who will be sending Type 1 LSAs?
2.  How many Type 1 LSA entries will there be?
3.  What will the "Link Count" be for each of the Type 1 LSA entries?
4.  Who will be sending Type 2 LSAs?
5.  How many Type 2 LSA entries will there be?

## Topology Diagram

[![topo](/assets/2015/04/topo2.png)](/assets/2015/04/topo2.png)

### Descriptions

You may recall that in [**Part 1**](/a-deep-dive-in-ospf-lsas-part-1/ "A Deep Dive in OSPF LSAs, Part 1") I mentioned the following (thanks to Wikipedia for the descriptions!):

*   **Type 1 – Router LSA** – each router announces its presence and lists the links to other routers or networks in the same area, together with the metrics to them. Type 1 LSAs are flooded across their own area only. The link-state ID of the type 1 LSA is the originating router ID.
*   **Type 2 – Network LSA** – the designated router (DR) on a broadcast segment (e.g. Ethernet) lists which routers are joined together by the segment. Type 2 LSAs are flooded across their own area only. The link-state ID of the type 2 LSA is the IP interface address of the DR.

## Q&As
### Question 1

_Who will be sending Type 1 LSAs?_

This is an easy one. As per the description above, "**each router** announces its presence...". Therefore the answer is all routers.

### Question 2

_How many Type 1 LSA entries will there be?_

As mentioned above, each router is responsible for sending its own Type 1 LSA. Therefore the answer is four, one per router.

### Question 3

_What will the "Link Count" be for each of the Type 1 LSA entries?_

Here's where things get a little tricky.

Looking at the diagram we can see the following:

*   R1 has **two** physical links - 10.1.2.0/30 and 10.1.23.0/24
*   R2 has **one **physical link - 10.1.2.0/30
*   R3 has **one **physical link - 10.1.34.0/24
*   R4 has **one** physical link - 10.1.34.0/24

However, if you gave the above answers you would only be half right.

As mentioned in [**Part 4**](/a-deep-dive-in-ospf-lsas-part-4/ "A Deep Dive in OSPF LSAs, Part 4"), Point-to-Point links advertise **two** links per connection. One which contains the RID of their neighbour and one which contains the network address of the link. Therefore the correct answer is:

*   R1 has **three** physical links - 10.1.2.0/30 and 10.1.23.0/24
*   R2 has **two **physical link - 10.1.2.0/30
*   R3 has **one **physical link - 10.1.34.0/24
*   R4 has **one** physical link - 10.1.34.0/24

### Question 4

_Who will be sending Type 2 LSAs?_

As per the description at the top of this post, " the **designated router** (DR) on a broadcast segment (e.g. Ethernet) lists which routers are joined together by the segment". Therefore the answer is R1.

### Question 5

_How many Type 2 LSA entries will there be?_

As there is only one broadcast segment in this topology, the answer is one (R1).

## Outputs

Now that we've submitted our answers for each of the questions, we can use the outputs below to verify our answers.

The answer to each question is colour coded in the following manner. All answers can be found in the LSDB but I have included the Type 1 and Type 2 LSAs for completeness:

*   Question 1 & 2
*   Question 3
*   Question 4 & 5

## LSDB

```
R1#sh ip os da

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         1157        0x80000003 0x003737 3
2.2.2.2         2.2.2.2         1193        0x80000002 0x00518E 2
3.3.3.3         3.3.3.3         1158        0x80000003 0x009120 1
4.4.4.4         4.4.4.4         1158        0x80000003 0x005355 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.34.1       1.1.1.1         68          0x80000005 0x0033AC
```

## Type 1 LSAs

```
R1(config-router)#do sh ip os da ro

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

  LS age: 1815
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 1.1.1.1
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000005
  Checksum: 0xE787
  Length: 60
  Number of Links: 3

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 2.2.2.2
     (Link Data) Router Interface address: 10.1.2.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.2.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 10.1.34.1
     (Link Data) Router Interface address: 10.1.34.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 1780
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 2.2.2.2
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000005
  Checksum: 0xFCE2
  Length: 48
  Number of Links: 2

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 1.1.1.1
     (Link Data) Router Interface address: 10.1.2.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 10.1.2.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 677
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 3.3.3.3
  Advertising Router: 3.3.3.3
  LS Seq Number: 80000005
  Checksum: 0x8D22
  Length: 36
  Number of Links: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 10.1.34.1
     (Link Data) Router Interface address: 10.1.34.3
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 760
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 4.4.4.4
  Advertising Router: 4.4.4.4
  LS Seq Number: 80000005
  Checksum: 0x4F57
  Length: 36
  Number of Links: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 10.1.34.1
     (Link Data) Router Interface address: 10.1.34.4
      Number of TOS metrics: 0
       TOS 0 Metrics: 10
```

## Type 2 LSA

```
R1(config-router)# do sh ip os da ne

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Net Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 873
  Options: (No TOS-capability, DC)
  LS Type: Network Links
  Link State ID: 10.1.34.1 (address of Designated Router)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000004
  Checksum: 0x35AB
  Length: 36
  Network Mask: /24
        Attached Router: 1.1.1.1
        Attached Router: 3.3.3.3
        Attached Router: 4.4.4.4
```

## Other Posts in this Series

See the [**A Deep Dive in OSPF LSAs - Index**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") post for links to all of the posts in this series.