---
title: Zone-Based Policy Firewall, Part 1
sub_heading: "We've covered of CBAC, now let's look at ZFW"
redirect_from: /zone-based-policy-firewall-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Security

tags:
- CBAC
- Firewall
- NBAR
- Security
- Cisco
- ZFW
---
Cisco's Zone-Based Policy Firewall (ZFW) can be quite confusing when you first start looking in to it, so over the next couple of blog posts I hope to provide readers with some useful information. Having said that, I'll do my best to avoid reinventing the wheel given that Cisco has already done a great job of [**documenting ZFW**](http://www.cisco.com/en/US/products/sw/secursw/ps1018/products_tech_note09186a00808bc994.shtml).

## ZFW Parts

ZFW is made up of several "parts", which, once all put together form a fully functional firewall. These parts are:

*   Zones & Zone Members
*   Class Maps
*   Policy Maps
*   Zone Pairs

## Zones & Zone Members

As the name suggests, ZFW requires that you put interfaces in to zones. The idea is that you put your interfaces in to zones and then apply inter-zone firewall rules which will permit or deny traffic flows.

There are two types of you need to be aware of:

*   User Configurable
*   "self"

User Configurable zones are zones which you configure yourself (dah!). How many you create, what you name them and how many interfaces you assign to the zones to is totally up to you. For example, if you have three interfaces which are used for LAN traffic and one interface which is used for WAN traffic, you may want to create a "LAN" zone and a "WAN" zone and put the interfaces in to their corresponding zones. Alternatively, you may use one interface for your LAN, another for your Servers and another for your WAN. You could then create a "Server" zone along with the "LAN" and "WAN" zone and have each interface allocated to its own zone.

The "self" zone is completely different to the User Configurable zone. For starters, you cannot rename it and you cannot allocate any interfaces to it. Instead, the "self" zone is a system zone which is dedicated to permitting and denying traffic destined for the router itself. For example, telnetting or SSH'ing in to the router to configure it.

Finally, a Zone Member is a the term used to describe an interface which has been put in to a zone. For example, if you put Fa0/0 in the "LAN" zone, it is a member of the "LAN" zone.

## Zone Traffic

Cisco's [**Rules For Applying Zone-Based Policy Firewall**](http://www.cisco.com/en/US/products/sw/secursw/ps1018/products_tech_note09186a00808bc994.shtml#topic3) explains what happens when traffic flows through these zones. However, I feel that their explanations may be a little too techy for those who are new to the subject so below I have provided my translations:

*   _A zone must be configured before interfaces can be assigned to the zone._
*   _An interface can be assigned to only one security zone._

You must define zones that you want to use (e.g "SERVER", "LAN", "WAN" etc). Once you have done that, you need to put your interfaces in to their corresponding zones. Note, interfaces can only be put in to a single zone.

*   _All traffic to and from a given interface is implicitly blocked when the interface is assigned to a zone, except traffic to and from other interfaces in the same zone, and traffic to any interface on the router._
*   _Traffic is implicitly allowed to flow by default among interfaces that are members of the same zone._

If you put **Fa0/0** in the "LAN" zone and **Fa0/1** in the "WAN" zone, hosts connected to these two interfaces **will not** be able to communicate by default. However, if both interfaces were in the same zone (e.g "WAN"), they **will** be able to communicate by default.

*   _In order to permit traffic to and from a zone member interface, a policy allowing or inspecting traffic must be configured between that zone and any other zone._

To allow two hosts who reside in different zones (e.g  Fa0/0 in the "LAN" zone and Fa0/1 in the "WAN" zone) you need to configure a policy.

*   _The self zone is the only exception to the default deny all policy. All traffic to any router interface is allowed until traffic is explicitly denied._

Unlike the situation mentioned above where traffic between **Fa0/0** in the "LAN" zone and **Fa0/1** in the "WAN" zone **is not** allowed by default, traffic which is destined for the router itself is allowed by default. In other words, hosts connected to Fa0/0 and Fa0/1 can telnet, ping, SSH, SNMP poll, etc the router without having to configure any policies.

*   _Traffic cannot flow between a zone member interface and any interface that is not a zone member. Pass, inspect, and drop actions can only be applied between two zones._

As mentioned previously, if **Fa0/0** and **Fa0/1** are in the same zone, they can communicate with one another by default. If they are in different zones they cannot communicate by default, but a policy can be configured to allow it. However, this dot point explains that if **Fa0/0** is in the "LAN" zone and **F0/1** has not been put in to any zone, hosts connected to these interfaces will not be able to communicate at all.

*   _If it is required that an interface on the box not be part of the zoning/firewall policy. It might still be necessary to put that interface in a zone and configure a pass all policy (sort of a dummy policy) between that zone and any other zone to which traffic flow is desired._
*   _From the preceding it follows that, if traffic is to flow among all the interfaces in a router, all the interfaces must be part of the zoning model (each interface must be a member of one zone or another)._

Situations may arise where you want to use ZFW on some interfaces but not others. For example, you want to use ZFW on your WAN interface but you don't deem it necessary to use it on your LAN and Server interfaces. As mentioned above you can't simply put your WAN interface in a ZFW zone and leave your LAN and Server interfaces without zone configurations as zoned and non-zoned interfaces cannot communicate. The solution to this problem is to put your LAN and Server interfaces in to a zone and configure it to "pass" all traffic. (I will expand on this point in my next blog post).

*   _The only exception to the preceding deny by default approach is the traffic to and from the router, which will be permitted by default. An explicit policy can be configured to restrict such traffic._

I'm not too sure why Cisco put this dot point at the end of the list given that it appears that they're making reference to the "self" zone dot point earlier on in the list. Anyway, this point is saying that "although traffic to and from the self zone is enabled by default, a policy can be implemented to restrict the traffic which is destined for the router.

Before I wrap this section up I must stress the importance of implementing a policy on the "self" zone. As mentioned above, all traffic sourced from and destined for the router (aka the "self" zone) is **permitted** by default. This means that if you've got telnet, SSH, HTTP, HTTPS, SNMP, FTP, etc enabled on your router, be aware that by default people on the internet can also attempt to exploit these protocols.

## Class Maps

Class Maps are used to to define the firewall rules. You can create as many as you like and use them on as many zones as you like (through the use of a Policy Map). Further to this, using the the **"match-any"** and **"match-all"** statements in your Class Maps allows for very granular rules.

## Policy Maps

Policy Maps are used to group multiple Class Maps in to a single policy. (More on this in my next blog post).

## Zone Pairs

In order to apply your firewall rules, you must attach a Policy Map to a Zone Pair. (This will also be expanded on in my next blog post).

Update
------

**[Part 2](/zone-based-policy-firewall-part-2/)** of this series has been published.