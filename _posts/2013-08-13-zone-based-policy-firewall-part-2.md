---
title: Zone-Based Policy Firewall, Part 2
sub_heading: "Let's continue on our ZFW journey"
redirect_from: /zone-based-policy-firewall-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Security

tags:
- CBAC
- Firewall
- Security
- Cisco
- ZFW
---
[**In my previous post**](/zone-based-policy-firewall-part-1/ "Zone-Based Policy Firewall, Part 1") I touched on the four parts (Zones & Zone Members, Class Maps, Policy Maps and Zone Pairs) which make up a ZFW configuration. In this post I will explain the "actions" which are used to tell the router how to handle inbound and outbound traffic flows.

## Actions

Actions are applied to Class Maps which as they're being nested in Policy Maps. (Don't worry if that sentence confuses you, it'll all become clear when I show you an example a little later on). [**As per Cisco's website**](http://www.cisco.com/en/US/products/sw/secursw/ps1018/products_tech_note09186a00808bc994.shtml#conf-zbf2), there are three actions to choose from:

*   **Drop** - Drops packets which are matched by the Class Map.
*   **Pass** - Allows packets which are matched by the Class Map through in one direction
*   **Inspect** - Works exactly like [**CBAC**](/tags/#cbac).

## Drop

The "drop" action does exactly what its name implies - it drops traffic which is matched by a Class Map. A great place to use this action is the connection between your WAN interface and the router's "self" zone. You might recall at the [**end of my last post**](/zone-based-policy-firewall-part-1/ "Zone-Based Policy Firewall, Part 1") I mentioned that all traffic destined for and sourced from the router (aka the "self" zone) is allowed by default. By implementing the "drop" action you can close off this security hole.

## Pass

Out of the three, the "pass" action is the most difficult to comprehend. It's not so bad, it's just that the other two are just really simple to understand.

The "pass" action allows traffic to flow in one direction only. If you'd like the return traffic to make it through the firewall, you must implement another "pass" rule which matches the return traffic. For example, if your PCs are connected to Fa0/0 ("LAN" zone) and your internet is connected to Fa0/1 ("WAN" zone), and you wanted to allow the PCs to access websites only, you'd need to do the following:

*   **Source Zone:** LAN-Zone
*   **Destination:** WAN-Zone
*   **Match Criteria:** Any Source IP & Any TCP Port Number to Any Destination IP & TCP Port Number 80

*   **Source Zone:** WAN-Zone
*   **Destination:** LAN-Zone
*   **Match Criteria:** Any Source IP & TCP Port Number 80 to Any Destination IP & Any TCP Port Number

As you can see, the two rules are mirrors of each other. The first rule allows the web request to go out and the next rule allows the web server's response back in. If you only implement one of the above rules the web browsing would not work.

You may be wondering why anyone would bother using the "pass" action when it would seem that the "inspect" action (see below) does exactly the same thing, but it only requires half the effort! Well, there are two reasons that I can think of off the top of my head.

The first reason is that "pass" uses less resources than the "inspect". This is because "pass" simply allows the traffic to go through, whereas "inspect" needs to track each and every connection and its state.

The second reason is that "inspect" cannot be used on traffic sourced from or destined for the router's "self" zone. This therefore means that if you want to control traffic to and from the router (rather than permitting it all as is the default), you can "pass" traffic which specifically matches your Class Maps. Doing this gives you the best of both worlds - it drops the traffic which is not supposed to be sent and received and passes the traffic which is.

## Inspect

Finally we have "inspect". Inspect does exactly what a lot of people expect out of their firewalls after dealing with home-grade routers which do SPI (Stateful Packet Inspection). It "watches" the traffic that goes out and only allows the specific return traffic back in. All other inbound traffic is dropped. Further to this, once the connection is closed the return traffic will no longer be allowed through the firewall and it too will be dropped.

To put it simply, specific firewall rules are dynamically created and removed as needed to ensure only requested traffic is allowed through the firewall. This provides a level of security which the "pass" action does not. This is because "pass", if configured in the Outside to Inside direction will allow all traffic in, regardless of whether it is return traffic which was requested by an Inside host or if it is new traffic which was initiated by an Outside host.

Now let's take a look at the example we saw in the "pass" section above. Again, let's say your PCs are connected to Fa0/0 ("LAN" zone) and your internet is connected to Fa0/1 ("WAN" zone), and you want to allow the PCs to access websites only. This is how you'd do it:

*   **Source Zone: **LAN-Zone
*   **Destination:** WAN-Zone
*   **Match Criteria:** Match Protocol HTTP

Here we can see two things - The first is that the required configuration is half the size of the "pass" configuration. The second thing is that there is a "match protocol" statement instead of an ACL-like statement. By using "match protocol" the router will is able to perform protocol specific firewalling where required - for example, where FTP is concerned. (If you're not too sure what I mean, have a quick read about Passive and Active FTP connections and the port numbers which they use).

## Summary

In conclusion there is no "best" action out of the three. They each have their uses and more often than not you may find that you use a combination of the three in all of your setups.