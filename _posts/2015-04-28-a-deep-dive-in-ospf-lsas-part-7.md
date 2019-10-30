---
title: A Deep Dive in OSPF LSAs, Part 7
sub_heading: "A series of posts to help you become an OSPF expert!"
redirect_from: /a-deep-dive-in-ospf-lsas-part-7/

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
So far in [**this series**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") I’ve covered Type 1, 2 and 3 LSAs. Next I’m going to cover Type 4 and Type 5 LSAs with the help of this topology.

[![topo1](/assets/2015/04/topo12.png)](/assets/2015/04/topo12.png)

Before we get started though, let’s have a quick refresher on what these LSAs are used for:

*   **Type 4 – ASBR-Summary LSA** – this is needed because Type 5 External LSAs are flooded to all areas and the detailed next-hop information may not be available in those other areas. This is solved by an Area Border Router flooding the information for the router (i.e. the Autonomous System Boundary Router) where the type 5 originated. The link-state ID is the router ID of the described ASBR for type 4 LSAs.
*   **Type 5 – External LSA** – these LSAs contain information imported into OSPF from other routing processes. They are flooded to all areas unchanged (except stub and NSSA areas). For “External Metric Type 1″ LSAs routing decisions are made using the Type 1 metric cost sent, as the total cost to get to the external destination and includes the cost to the ASBR; while for “External Type 2″ LSAs the metric sent is the cost from the ASBR to the External destination network and must be added to the OSPF cost to the ASBR advertising the Type 5. The link-state ID of the type 5 LSA is the external network number.

To put the above explanations in another way:

*   **Type 4 LSAs** are sent by the ABR. The ABR uses these LSAs to tell the rest of the network that they've got an ABSR in their area, and that those other routers can reach the ASBR through them. In the topology above, this means that R2 will tell R1 “I’ve got an ASBR in Area 23 and its RID is 3.3.3.3. Send your data to me if you need to reach its external routes”.
*   **Type 5 LSAs** are sent by the ASBRs. The ASBRs use these LSAs to tell the rest of the network which external routes (aka redistributed routes) they’re able to reach. In the topology above, this means that R3 will tell the rest of the network “I can reach 10.3.4.0/24”.

Now that we've covered the theory, let’s jump into the lab.

On R3, the 10.3.4.0/24 network is redistributed into OSPF using the following commands:

```
R3(config-router)# router ospf 1
R3(config-router)# redistribute connected subnets
```

As the subnet has been redistributed into OSPF, by default, it will be advertised as an “External Type 2″ route, as we see in R2’s routing table below:

```
R2#sh ip route | b Gate
Gateway of last resort is not set

     10.0.0.0/24 is subnetted, 3 subnets
C       10.1.2.0 is directly connected, FastEthernet0/0
C       10.2.3.0 is directly connected, FastEthernet0/1
O E2    10.3.4.0 [110/20] via 10.2.3.3, 00:28:47, FastEthernet0/1
```

As discussed above, because we have external routes in our topology we should see Type 4 and Type 5 LSAs. Let’s take a look at R2’s LSDB:

```
R2#sh ip os da

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         259         0x80000004 0x0049B9 1
2.2.2.2         2.2.2.2         225         0x80000004 0x000EEA 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.1        1.1.1.1         259         0x80000002 0x0057C3

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.3.0        2.2.2.2         225         0x80000003 0x00F128

                Summary ASB Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
3.3.3.3         2.2.2.2         225         0x80000002 0x001706

                Router Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum Link count
2.2.2.2         2.2.2.2         225         0x80000004 0x0042B1 1
3.3.3.3         3.3.3.3         254         0x80000004 0x0007E2 1

                Net Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.3.2        2.2.2.2         227         0x80000002 0x006C9F

                Summary Net Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.0        2.2.2.2         227         0x80000003 0x000913

                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
10.3.4.0        3.3.3.3         1878        0x80000001 0x00BAC8 0
```

Sure enough, we see both Type 4 and Type 5 LSAs. But wait a minute, why is there a Type 4 LSA in Area 0, but no Type 4 LSA in Area 23? The answer is it’s not needed. This is because all of the routers in Area 23 already know how to reach R3 because it already flooded the area with its Type 1 LSA (see [**Part 2**](/a-deep-dive-in-ospf-lsas-part-2/ "A Deep Dive in OSPF LSAs, Part 2") for more information).

For completeness, let’s take a look at the contents of the Type 4 and Type 5 LSAs.

## Type 4 LSA

Below is the contents of the Type 4 LSA:

```
R2#sh ip os da asbr-summary

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Summary ASB Link States (Area 0)

  LS age: 675
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(AS Boundary Router)
  Link State ID: 3.3.3.3 (AS Boundary Router address)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000002
  Checksum: 0x1706
  Length: 28
  Network Mask: /0
        TOS: 0  Metric: 10
```

## Type 5 LSA

Below is the contents of the Type 5 LSA:

```
R2#sh ip os da external

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Type-5 AS External Link States

  Routing Bit Set on this LSA
  LS age: 461
  Options: (No TOS-capability, DC)
  LS Type: AS External Link
  Link State ID: 10.3.4.0 (External Network Number )
  Advertising Router: 3.3.3.3
  LS Seq Number: 80000002
  Checksum: 0xB8C9
  Length: 36
  Network Mask: /24
        Metric Type: 2 (Larger than any link state path)
        TOS: 0
        Metric: 20
        Forward Address: 0.0.0.0
        External Route Tag: 0
```

Everything here seems pretty stock standard except perhaps for the Forward Address. This represents the address of the ASBR, however, if ASBR resides in this area, it will be set to 0.0.0.0. I'll cover this in more detail in a future post.

## External Route Summarisation (Type 5 LSAs)

In [**Part 6**](/a-deep-dive-in-ospf-lsas-part-6/ "A Deep Dive in OSPF LSAs, Part 6") I demonstrated how you can summarise inter-area (Type 3) routes. You can achieve similar results with External (Type 5) routes using a slightly different command.

First, we’ll need to add a couple of new routes to on R3, like so:

[![topo2](/assets/2015/04/topo22.png)](/assets/2015/04/topo22.png)

As we’ve already issued the “redistribute connected” command on R3, there’s nothing left to do in order to get those routes advertised. Let’s take a look at R2 Type 5 LSAs just to be sure:

```
R2#sh ip os da | b Type-5
                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
10.3.4.0        3.3.3.3         1184        0x80000002 0x00B8C9 0
10.3.5.0        3.3.3.3         187         0x80000001 0x00AFD2 0
10.3.6.0        3.3.3.3         173         0x80000001 0x00A4DC 0
10.3.7.0        3.3.3.3         58          0x80000001 0x0099E6 0

OK great, the new Type 5 LSAs are there. Now all we need to do is summarise them.

The inter-area summarisation we saw in [**Part 6**](/a-deep-dive-in-ospf-lsas-part-6/ "A Deep Dive in OSPF LSAs, Part 6") had us implement the summary route on the ABR. However, external route summarisation needs to be done on the ASBR. Therefore, we need to put the following commands on R3:

R3(config)#router os 1
R3(config-router)#summary-address 10.3.4.0 255.255.252.0
```

Now let’s take a look at R2’s LSDB:

```
R2#sh ip os da | b Type-5
                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
10.3.4.0        3.3.3.3         24          0x80000003 0x00A7DC 0
```

As we can see above, only the summarised route is being advertised now. As per the output below, the content of the summarised route’s LSA (Type 5) is exactly the same as the Type 5 LSA we saw above, only the network address and mask is different.

```
R2#sh ip os da ex

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Type-5 AS External Link States

  Routing Bit Set on this LSA
  LS age: 31
  Options: (No TOS-capability, DC)
  LS Type: AS External Link
  Link State ID: 10.3.4.0 (External Network Number )
  Advertising Router: 3.3.3.3
  LS Seq Number: 80000003
  Checksum: 0xA7DC
  Length: 36
  Network Mask: /22
        Metric Type: 2 (Larger than any link state path)
        TOS: 0
        Metric: 20
        Forward Address: 0.0.0.0
        External Route Tag: 0
```

## Other Posts in this Series

See the [**A Deep Dive in OSPF LSAs - Index**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") post for links to all of the posts in this series.