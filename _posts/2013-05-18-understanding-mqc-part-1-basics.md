---
title: 'Understanding MQC, Part 1: The Basics'
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /understanding-mqc-part-1-basics/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- QoS

tags:
- QoS
- GNS3
- Labs
- Cisco
- Shaping
---
Over the course of my next few blog posts I will be covering Modular QoS CLI (MQC), Weighted Fair Queue (WFQ), Class-Based Weighed Fair Queuing (CBWFQ) and Low Latency Queuing (LLQ).

Configuring MQC is not an easy job for those who have never done it before, but thankfully once you understand the basics it's not very difficult to delve deeper. So let's get started!

## Most Important Rule of QoS

I need to stress the most important rule of QoS - That is, QoS is not implemented if there is no congestion on the link. This is because if there is no congestion on the link, then packets are being sent as soon as they're being received, therefore there is no need for QoS to kick in.

It is a simple rule to understand but it is easily forgotten.

## DSCP

DSCP values are used to determine how traffic types should be handled. There is the priority value called Expedite Forwarding (EF) which is used to mark traffic which is latency sensitive, such as Voice. Then there is the Assured Forwarding (AF) values which define varying amounts of prioritisation and drop probability. (This Wikipedia [**table**](http://en.wikipedia.org/wiki/Differentiated_services#Assured_Forwarding_.28AF.29_PHB_group) is well worth a look). Finally there is the Class Selector (CS) values.

## Class Maps & Policy Maps

MQC's configuration is all about Class Maps and Policy Maps. To sum each of them up:

*   **Class Maps** - There are two types of Class Maps - one is used to classify (also known as mark) traffic, while the other is used to assign a portion of bandwidth to the marked traffic. They are applied inbound and outbound respectively through the use of Policy Maps.
*   **Policy Maps** - Policy Maps contain multiple Class Maps. As mentioned above, they are applied to interfaces in the outbound and inbound directions. They are also used to [**shape an interface's speed**](/shape-average-vs-shape-peak-part-1/ "Shape Average Vs Shape Peak – Part 1") as agreed with your carrier.

The above is expanded on below.

## Diagram

[![topology](/assets/2015/02/topology.png)](/assets/2015/02/topology.png)

## Step by Step

Let's take a look at the steps which must be taken in order to achieve a QoS setup:

1.  The "classify" **Class Maps** are written to match traffic types/groups. For example, all Backup traffic would be matched in one Class Map while all Voice traffic would be matched in another.
2.  These **Class Maps** are then put in to a **Policy Map**. From here the **Class Maps** are given appropriate markings. For example, mark the Voice Class Map as EF and the backup Class Map as AF33. This **Policy Map** is then placed **inbound** on the router's LAN facing interface (Fa0/0 in the image above).
3.  The "prioritisation" **Class Maps** are then written to match the DSCP values marked in Step 2.
4.  The above mentioned **Class Maps** are then put in to a **Policy Map** which assigns each one an appropriate amounts of bandwidth. For example, for all packets which match the Voice Class Map, give them 150kb of bandwidth to share. (Note that unlike the inbound Policy Map mentioned above, this Policy Map is not applied to any of the router's interfaces).
5.  A third **Policy Map** is created. This one is used to [**shape the interface's speed**](/shape-average-vs-shape-peak-part-1/ "Shape Average Vs Shape Peak – Part 1") to the rate which has been agreed with your carrier. This Policy Map is applied **outbound** on the router's WAN facing interface (Fa0/1 in the image above).
6.  Finally, the **Policy Map** mentioned in Step 4 is configured as a "child" policy of the Policy Map mentioned in Step 5.

You might be wondering why outbound traffic requires two Policy Maps. As mentioned in Step 5, the first one is used to shape the interface's speed. The reason why this needs to be done is due to the "Most Important Rule of QoS" mentioned above.

For example, if you purchased a 2mb Ethernet service from your carrier and had it terminated on your router's gigabit interface, your router will believe that it has a gigabit connection to the carrier. This therefore means that your router will never use the QoS policies as it will never think there is any congestion. However, by implementing a shaper you can tell the router _"although the carrier connection is terminated on a gigabit port, we can only use 2mb/sec"_. The router then disregards the fact that it is using a gigabit interface and instead  treats it as a 2mb interface, therefore enforcing the QoS policies in times of congestion.

Now that the basics are covered, [**my next post**](/understanding-mqc-part-2-basic-configuration/ "Understanding MQC, Part 2: Basic Configuration") will cover basic configurations.