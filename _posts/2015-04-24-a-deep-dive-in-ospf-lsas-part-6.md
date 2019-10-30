---
title: A Deep Dive in OSPF LSAs, Part 6
sub_heading: "A series of posts to help you become an OSPF expert!"
redirect_from: /a-deep-dive-in-ospf-lsas-part-6/

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
Up until this point I have only covered Type 1 and Type 2 LSAs in [**this series**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index"). I'm guessing by now you must be quite familiar with them and are interested to hear about the other LSAs, so let's move onto Type 3's.

This is what Wikipeda has to say about Type 3 LSAs:

*   **Type 3 – Summary LSA** – an Area Border Router (ABR) takes information it has learned on one of its attached areas and summarizes it before sending it out on other areas it is connected to. This summarization helps provide scalability by removing detailed topology information for other areas, because their routing information is summarized into just an address prefix and metric. The summarization process can also be configured to remove a lot of detailed address prefixes and replace them with a single summary prefix, helping scalability. The link-state ID is the destination network number for type 3 LSAs.

While the above is true, the summarisation must be configured manually on the ABR, so I'll delve deeper into it later on in this post. For now let's just focus on the fact that an ABR is used to share routes between the areas which it is connected. Let's use the topology below to demonstrate this:

[![topo1](/assets/2015/04/topo11.png)](/assets/2015/04/topo11.png)

As R1 resides in Area 0 and R3 resides in Area 23, they cannot use Typ1 LSAs to learn about 10.1.2.0/24 and 10.2.3.0/24 respectively. This is because Type 1 LSAs are only flooded in the area which the router resides.

Since R2 is on the border of both Area 0 and Area 23, we'll need it to advertise the routes between the two areas. It is able to do this by using Type 3 LSAs.

Let's take a look at the LSDBs:

```
R2(config-router)#do sh ip os da

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         740         0x80000007 0x0043BC 1
2.2.2.2         2.2.2.2         735         0x8000000A 0x0002F0 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.1        1.1.1.1         740         0x80000004 0x0053C5

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.3.0        2.2.2.2         735         0x80000004 0x00EF29

                Router Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum Link count
2.2.2.2         2.2.2.2         735         0x80000007 0x003CB4 1
3.3.3.3         3.3.3.3         698         0x80000007 0x00FAED 1

                Net Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.3.2        2.2.2.2         737         0x80000004 0x0068A1

                Summary Net Link States (Area 23)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.0        2.2.2.2         737         0x80000006 0x000316
```

Highlighted above we see two new things which we haven't seen before in this series of posts.

1.  Two different areas on one router (Area 0 and Area 23)
    *   This is because the "show ip ospf database" command was issued on the ABR.
2.  The "Summary Net Links" section of the LSDB.
    *   This is because there is more than one area in this topology. All previous examples involved routers which only resided in Area 0.

Further to this, we see R2 is advertising 10.2.3.0/24 (which resides in **Area 23**), into **Area 0**. We also see R2 is  advertising 10.1.2.0/24 (which resides in **Area 0**), into **Area 23**. In other words, we're seeing this:

[![topo2](/assets/2015/04/topo21.png)](/assets/2015/04/topo21.png)

By employing Type 3 LSAs, all routers now know how to reach all routes.

## Type 3 LSA Details

For completeness, below is the contents of R2's Type 3 LSAs:

```
R2(config-router)#do sh ip os da su

            OSPF Router with ID (2.2.2.2) (Process ID 1)

                Summary Net Link States (Area 0)

  LS age: 428
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 10.2.3.0 (summary Network Number)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000005
  Checksum: 0xED2A
  Length: 28
  Network Mask: /24
        TOS: 0  Metric: 10


                Summary Net Link States (Area 23)

  LS age: 428
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 10.1.2.0 (summary Network Number)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000007
  Checksum: 0x117
  Length: 28
  Network Mask: /24
        TOS: 0  Metric: 10 
```

## Inter-Area Summarisation (Type 3 LSAs)

Now that we've covered the distribution of Type 3 LSAs, let's look at summarisation in action. First though, let's add a few more routes to the existing topology:

[![topo-new](/assets/2015/04/topo-new.png)](/assets/2015/04/topo-new.png)

Before we make any other changes, let's take a look at the routing table on R1:

```
R1#sh ip route | b Gate
Gateway of last resort is not set
     10.0.0.0/24 is subnetted, 5 subnets
O IA    10.2.0.0 [110/30] via 10.1.2.2, 00:01:43, FastEthernet0/0
O IA    10.2.1.0 [110/30] via 10.1.2.2, 00:01:43, FastEthernet0/0
C       10.1.2.0 is directly connected, FastEthernet0/0
O IA    10.2.2.0 [110/30] via 10.1.2.2, 00:01:43, FastEthernet0/0
O IA    10.2.3.0 [110/20] via 10.1.2.2, 00:10:14, FastEthernet0/0
```

Let's also take a look at R1's LSDB:

```
R1#sh ip os da

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         1627        0x80000003 0x004BB8 1
2.2.2.2         2.2.2.2         1627        0x80000003 0x0010E9 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.1        1.1.1.1         1627        0x80000001 0x0059C2

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.0.0        2.2.2.2         2           0x80000004 0x00759C
10.2.1.0        2.2.2.2         6           0x80000001 0x0070A3
10.2.2.0        2.2.2.2         6           0x80000001 0x0065AD
10.2.3.0        2.2.2.2         6           0x80000001 0x00F526

                Summary ASB Link States (Area 0)
          
Link ID         ADV Router      Age         Seq#       Checksum
3.3.3.3         2.2.2.2         494         0x80000001 0x001905
```

In the output above we can see that R1 (Area 0) sees each route from Area 23 individually. To reap the benefits of summarisation, let's combine these routes into a single 10.2.3.0/22 route. To achieve this, we need to issue the following command on R2, the ABR of Area 23:

```
R2(config)#router ospf 1
R2(config-router)#area 23 range 10.2.0.0 255.255.252.0
```

Now let's take a look at R1's routing table:

```
R1#sh ip route | b Gate
Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
O IA    10.2.0.0/22 [110/20] via 10.1.2.2, 00:01:07, FastEthernet0/0
C       10.1.2.0/24 is directly connected, FastEthernet0/0
```

Let's also take a look at R1's LSDB:

```
R1#sh ip os da

            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         1871        0x80000003 0x004BB8 1
2.2.2.2         2.2.2.2         1872        0x80000003 0x0010E9 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.2.1        1.1.1.1         1871        0x80000001 0x0059C2

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.2.0.0        2.2.2.2         15          0x80000005 0x00FF1E

                Summary ASB Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
3.3.3.3         2.2.2.2         739         0x80000001 0x001905
```

We can see that both the routing table and LSDB have been reduced in size.This results in lower resource utilisation on routers in Area 0, as well as more stable network.

## Other Posts in this Series

See the [**A Deep Dive in OSPF LSAs - Index**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") post for links to all of the posts in this series.