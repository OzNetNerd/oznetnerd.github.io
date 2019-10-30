---
title: NBAR and its Many Uses
sub_heading: "Access Control, URL Filtering and QoS, oh my!"
redirect_from: /nbar-many-uses/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Security
- Misc.

tags:
- CBAC
- Filter
- Firewall
- NBAR
- QoS
- Security
- Cisco
---

NBAR, also known as Network Based Application Recognition is an invaluable tool that many people do not know exists or simply just don't use it enough.

As the name suggests, NBAR reads packets that flow through a router and "recognises" the types of applications that are sending the packets. Examples of applications that can be recognised include:

*   FTP
*   Kazaa
*   HTTP URL Addresses
*   Bit Torrent
*   Many, many more!

And the good news is that this handy little feature can be put to use in many different ways, some of which I have discussed below.

### NBAR & Access Control

Before NBAR came along, one of the techniques Network Administrators woulduse to stop users from getting up to mischief was blocking "bad" ports. However, with a little bit of know how it is very easy to change ports thereby making the "bad" traffic look like legitimate traffic. By using NBAR you don't need to specify port numbers, you just specify the application that you would like to block, apply it to an interface and you're done. Because NBAR reads the packets (up to, and including Layer 7 information), it is still able to block the traffic even if the users change their port numbers.

**Note:** NBAR can be used in conjunction with Context-Based Access Control ([**CBAC**](/tag/cbac/)) to create an extremely effective firewall on a Cisco router.

### NBAR & URL Filtering

NBAR can also be used to block URLs. This can be a great little feature if you have a small office setup and do not have the time or the resources to set up a proxy server.

The filtering also allows you to use wildcard masks so blocking websites with multiple sub-domains (such as YouTube) can be done just as easily as blocking a single domain.

### NBAR & QoS

Another application for NBAR is with QoS policies. Rather than specifying port numbers for different traffic types, NBAR will automatically identify the port numbers for you. This is a small gain I know, but it makes life a little easier. It also makes your config somewhat more readable in that instead of creating a class-map and ACL that specifies TCP port 80 and 23, you can use NBAR to match "HTTP" traffic and "Telnet" instead.