---
title: CBAC Firewall
sub_heading: "Context-Based Access Control - Cisco's built in stateful firewall"
redirect_from: /cbac-firewall/

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
- ACL
---
In my [**previous post**](/nbar-many-uses/) I mentioned the Cisco IOS firewall feature known as [**CBAC**](/tags/#cbac) (Context-Based Access Control). Today I will describe it in more detail and explain how you can use it to increase the security of your network.

As you may know, a firewall is used to protect your network from the outside world and all of the nasty hackers out there. With each passing day hackers' intrusion techniques are become more advanced and highly complex, and unfortunately basic ACLs are simply not good enough to protect you anymore. Thankfully CBAC (more commonly known to the wider I.T community as Stateful Packet Inspection, (SPI)) has come to the rescue.

CBAC, with the help of NBAR, reads the packets coming in to and going out of your network and creates dynamic ACL entries that will either permit or deny these traffic flows.

For example, if you want to allow ICMP and HTTP traffic (pings and internet browsing) out of your network, you would have to create an "outbound" access list on your internet facing interface. Then, to allow the returning traffic to re enter your network you would have to create an "inbound" access list, however, this is where your potential troubles start. This is because unless your "inbound" access list specifies all of the IP addresses that you intend to ping to or surf to, you are going to have to allow all ICMP and HTTP traffic in to your network, regardless of the source,which is not very secure.

Fortunately, as mentioned above CBAC has come to the rescue. With its dynamic ACL creations all you have to do is create the "outbound" access list which specifies what your users can and cannot do and CBAC does the rest. This is because CBAC watches the traffic that leaves your network and then creates a dynamic ACL to allow the returning traffic (and only the returning traffic) back in to the network.

See my [**CBAC in Action, Part 1**](/cbac-action-part-1/) and [**CBAC in Action, Part 2**](/cbac-action-part-2/) posts to see a real life example of how you can configure CBAC in your network.