---
title: Cisco UCS From the Ground Up - A Beginner's Guide Part 4
sub_heading: "A series of posts to help you become a UCS expert!"
redirect_from: /cisco-ucs-from-the-ground-up-a-beginners-guide-part-4/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- UCS

tags:
- Cisco
- UCS
---
OK, so in [**Part 1**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-1/ "Cisco UCS From the Ground Up – A Beginner’s Guide Part 1") and [**Part 2**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-2/ "Cisco UCS From the Ground Up – A Beginner’s Guide Part 2") I gave high level overviews of the UCS components. In [**Part 3**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-3/ "Cisco UCS From the Ground Up – A Beginner’s Guide Part 3") I did it all over again, but went a little deeper that time around. In this post, I'll be doing it again, but this time going even deeper.

Why you ask? Because diving too deeply into UCS too quickly is enough to confuse anyone. By going over the same components over and over again, getting a little deeper each time, I'm hoping to avoid causing confusion. I feel doing it this way is a better approach rather than writing one overview post then diving into why using a VIC 1240 and VIC 1280 may not necessarily give you 80Gb of bandwidth unless you've an appropriate FEX/IOM and enough FI uplinks :)

## 80Gb of Bandwidth!?!

Yes, that's right. With the **right combination** (I can't stress that enough) of equipment, your blades will be able to achieve 80Gb of bandwidth **each**.

The reason why I'm stressing this point is because I often see unfortunate situations where people have purchased parts which are capable of 80Gb, but they end up being confused and disappointed when they see that they're only achieving a fraction of that. I hope to prevent that from happening to others in the future with the next few sections of this post.

## What's Required?

As discussed a few times in this series of posts, the five main components to a UCS System when using B-Series servers are as follows:

1.  Fabric Interconnects
2.  Chassis
3.  IOMs
4.  Blade Servers
5.  VIC Cards

Unfortunately achieve 80Gb of bandwidth per blade isn't always as simple as buying 80Gb components, and that's the trap some people fall into. For example, the way you achieve 80Gb per blade on a M2 series servers is different to the way you achieve it when using M3 series servers. I believe it is this difference that may be the source of some confusion which results in people buying the wrong components.

**Note:** As with my previous post, it may help to keep an eye on the below diagram (taken from [**this**](http://www.definethecloud.net/hp-flex-10-cisco-vic-and-nexus-1000v/) Define the Cloud post) to track which part of the topology I’m talking about.

[![dtc](/assets/2015/02/dtc.png)](/assets/2015/02/dtc.png)

## Chassis

*   The UCS chassis (obviously) can deliver up to 80 Gb per blade.
*   You don't need to do anything to the chassis in order to make it capable of supporting the 80Gb.

## **IOMs**

*   The chassis consist of two IOMs slots. These IOMs have 4x 10Gb-KR Data Centre Ethernet (DCE) lanes **per blade**. This results in 40Gb per blade.
*   As mentioned previously, each IOM connects to each blade. This means that there are a total of 8x 10Gb-KR DCE lanes **per blade**. This results in 80gB per blade.

_Some additional information just in case you're interested:_

*   This means that the total number of DCE KR lanes per IOM is: 4 lanes * 8 blades = 32 - as there are two IOMs, this results in there being 64 10GB lanes per chassis.
*   The connection that exists between the IOMs and the blades is called a Host Facing Interface (HIF) from the IOM's perspective, and an adapter port from the blade's perspective.

## Choosing your IOMs

Given the above, we can see that the chassis is ready to provide 80Gb straight out of the factory. We can also see that there are enough 10GB DCE lanes there to enable your IOMs to provide your blades with 40Gb of bandwidth each. Therefore, (because you must use two IOMs), the IOMs have the capability to provide 80Gb of bandwidth to the blades too.

However, not all IOMs use all lanes. Therefore IOMs which don't use all lanes cannot provide 80Gb of bandwidth. This means that even if the other four components do support 80Gb, you will be limited to the speed provided by the IOM.

Let's take a look at a few of the IOMs which have been released over the years:

*   **2104XP:** Uses one lane (HIF) per blade = 10Gb * 2 IOMs = **20Gb**
*   **2204XP:** Uses two lanes (HIF) per blade = 20Gb * 2 IOMs = **40Gb **
*   **2208XP:** Uses four lanes (HIF) per blade = 40Gb * 4 IOMs = **80Gb**

Therefore, if we want to achieve 80Gb at the IOM level, we'll need a 2208XP.

## VICs

Up until this point we've got a chassis which is capable of 80Gb per blade. We've also got two IOMs which are each able to provide 40Gb to the blades. Because these two  IOMs connect to all blades we get a total of 80Gb at the IOM level as well.

Let's now take a look at a few of the VICs which have been released over the years:

*   **M81KR:** Uses one lane (adapter port) per IOM = 10Gb * 2 IOM = **20Gb**
*   **1240:** Uses two lanes (adapter ports) per IOM = 20Gb  * 2 IOM = **40Gb**
*   **1280:** Uses four lanes (adapter ports) per IOM = 40Gb * 2 IOM = **80Gb**

Choosing the right VIC is easy then, isn't it? If we want to achieve 80Gb at the VIC level we should just purchase a VIC 1280, shouldn't we? The answer is, it depends. As mentioned above "the way you achieve 80Gb per blade on a M2 series servers is different to the way you achieve it when using M3 series servers." So with that in mind, let's not choose which VIC(s) we'll buy just yet.

**Spoiler Alert:** If you're using an M2 series blade, using a single VIC1280 will give you 80Gb. However, if you're using an M3 series blade, you'll either need x2 VIC1240s, or x1 VIC1240 and x1 VIC1280. Read on to find out why.

## Choosing your VIC(s)
### M2 Series Blades

*   The M2 blades are very straightforward. The half width blades have a single mezzanine slot which means all eight of the 10GB DCE lanes will be connected directly between the VIC and the IOM.
*   This means that if you use a VIC1280 in conjunction with two 2208XP IOMs, you'll get 40Gb per IOM, which results in a total of 80Gb of bandwidth per blade.

_Some additional information just in case you're interested:_

*   Note that the four DCE lanes between IOM A and the VIC1280 are automatically port channeled. The same is true for the four lanes between IOM B and the VIC1280. (I'll expand on this further in my next post).

### M3 Series Blades

*   The M3 blade is a little more complex than the M2. It has a mezzanine slot as well as an mLOM slot, and the lanes are split evenly between them.
*   In other words, unlike the M2's mezzanine slot where all eight 10Gb DCE lanes connected to it, the M3's mezzanine slot only has four (which results in 20Gb per IOM, and a total of 40Gb per blade). The remaining four DCE lanes are connected to the mLOM (which again results in 20Gb per IOM, and a total of 40Gb per blade).
*   If were to use same hardware as we did in the M2 blade above, (the 2208XP IOM and one VIC1280), we'd only get 20Gb per IOM, therefore a total of 40Gb of bandwidth. Half the bandwidth of an M2 blade using the same IOM and VIC.
*   To achieve 80Gb on an M3 blade, you have two options:

1.  Use a 2208X and install a VIC1280 into the mezzanine slot, as well as a VIC1240 into the mLOM.
2.  Use a 2208X and install a VIC1240 into the mezzanine slot, install a port extender into the mLOM and then install a VIC1240 into the port extender.

*   Note that there are differences between these two options:
    *   Using the first option provides VIC redundancy whereby the blade can still use both IOMs if either of the two VICs failed. However, with the second option, the VIC1240 in the port extender relies on the VIC1240 in the mezzanine slot. If the VIC in the mezzanine slot fails, the VIC in the port extender can no longer operate.

*   _Some additional information just in case you're interested:_

*   The other difference between these two options is the automatic port channels which are formed between the IOMs and the VICs.
    *   With the first option, two port channels are created per IOM, each consisting of two ports (in other words, four port channels in total). The two DCE lanes which connect between IOM A and the VIC1280 create their own port channel, as do the two DCE lanes which connect Fabric A to the VIC1240. The same is done with IOM B. As mentioned above, this results in four port channels between the two VICs and the two IOMs.
    *   With the second option, the VIC1240 in the mezzanine slot and the VIC1240 in the port extender are physically linked inside the blade. This results in their DCE lanes being port channeled on a per IOM basis. This results in two port channels (one per IOM) with each port channel consisting of four DCE lanes (two per VIC).
    *   (Don't worry if this part confused you, I'll expand on it [**in my next post**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-5/)).

## Other Posts in this Series

See the [**Cisco UCS From the Ground Up – A Beginner’s Guide – Index**](/cisco-ucs-from-the-ground-up-index/ "Cisco UCS From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.