---
title: A Deep Dive in OSPF LSAs, Part 1
sub_heading: "A series of posts to help you become an OSPF expert!"
redirect_from: /a-deep-dive-in-ospf-lsas-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- OSPF

tags:
- Cisco
- OSPF
- LSAs
---
Configuring an OSPF network can be very easy. Within a couple of commands you can have a fully functioning network. While being easy to configure is definitely a pro, the con is that you don't actually need to understand how it works in order to use it. Why is this a problem you ask? Well, how can you troubleshoot something you don't understand? :)

## LSA Types

At the heart of OSPF is its LSAs. If you've forgotten what they do, here's a quick recap courtesy of Wikipedia:

*   **Type 1 - Router LSA** - each router announces its presence and lists the links to other routers or networks in the same area, together with the metrics to them. Type 1 LSAs are flooded across their own area only. The link-state ID of the type 1 LSA is the originating router ID.

*   **Type 2 - Network LSA** - the designated router (DR) on a broadcast segment (e.g. Ethernet) lists which routers are joined together by the segment. Type 2 LSAs are flooded across their own area only. The link-state ID of the type 2 LSA is the IP interface address of the DR.

*   **Type 3 - Summary LSA** - an Area Border Router (ABR) takes information it has learned on one of its attached areas and summarizes it before sending it out on other areas it is connected to. This summarization helps provide scalability by removing detailed topology information for other areas, because their routing information is summarized into just an address prefix and metric. The summarization process can also be configured to remove a lot of detailed address prefixes and replace them with a single summary prefix, helping scalability. The link-state ID is the destination network number for type 3 LSAs.

*   **Type 4 - ASBR-Summary LSA** - this is needed because Type 5 External LSAs are flooded to all areas and the detailed next-hop information may not be available in those other areas. This is solved by an Area Border Router flooding the information for the router (i.e. the Autonomous System Boundary Router) where the type 5 originated. The link-state ID is the router ID of the described ASBR for type 4 LSAs.

*   **Type 5 - External LSA** - these LSAs contain information imported into OSPF from other routing processes. They are flooded to all areas unchanged (except stub and NSSA areas). For "External Metric Type 1" LSAs routing decisions are made using the Type 1 metric cost sent, as the total cost to get to the external destination and includes the cost to the ASBR; while for "External Type 2" LSAs the metric sent is the cost from the ASBR to the External destination network and must be added to the OSPF cost to the ASBR advertising the Type 5. The link-state ID of the type 5 LSA is the external network number.

*   **Type 7 - Routers in a Not-so-stubby-area (NSSA)** - do not receive external LSAs from Area Border Routers, but are allowed to send external routing information for redistribution. They use type 7 LSAs to tell the ABRs about these external routes, which the Area Border Router then translates to type 5 external LSAs and floods as normal to the rest of the OSPF network.

## Demos

The aim of this series of blog posts is to show the above mentioned LSAs in action. To do this I'll enlist the help of a handful of lab topologies and a whole lot of "show" command outputs.

## Other Posts in this Series

See the [**A Deep Dive in OSPF LSAs - Index**](/a-deep-dive-in-ospf-lsas-index/ "A Deep Dive in OSPF LSAs – Index") post for links to all of the posts in this series.