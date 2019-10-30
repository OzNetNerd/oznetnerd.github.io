---
title: GNS3 + Real Cisco Gear
sub_heading: "Combine GNS3 with real equipment to create massive labs"
redirect_from: /gns3-real-cisco-gear/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs

tags:
- GNS3
- Labs
- Cisco
---
For those of you who have never heard of **[GNS3](http://www.gns3.net/)** before, you are in for a treat!

What it does is allow you to run multiple routers on your computer in a virtualised environment, and, because it uses real Cisco IOSes you are not limited to a short group of commands as you are when using simulators, you actually have access to the entire command tree as if the IOS was put on a real router.

At risk of stating the obvious, this is a huge asset for anyone who is interested in networking. Whether you are a network engineer, studying for a certification or just an enthusiast, this application is perfect for you.

Another big advantage is the amount of money it can save you. Rather than spend a couple of hundred dollars on a router, using GNS3 you can simply whip up one, two, ten or more in an instant! Imagine the complex topologies you could create with that!

Now I know you must be thinking "this is too good to be true", and, when something is too good to be true it usually is, so I will let you in on one (very small) piece of bad news. GNS3 is unable to emulate switches. That is because switches do most of their work in hardware ASICs whereas the routers that GNS3 emulates do most of their work in software. Speaking of which, this leads me to my next (very small) bit of bad news. GNS3 is not able to support every single router released, however, to its defence, it does support a large number of them. For information on both of these downfalls, see the **[FAQ](http://www.gns3.net/phpBB/topic18.html)** page.

Now, back to the positives. Although GNS3 cannot emulate switches, some of the routers it supports do support ethernet modules, so if you really want to test your Layer 2 skills in GNS3 you could always set up a few routers with switching modules and away you go.

And for those of you out there with existing labs, this post relates to you too! Why you ask? Because you can connect your physical equipment to your virtual lab. As you will see in my future posts, you are able to create a complex Layer 3 network in the virtual network and then connect it to your real router, switch or multi layer switch, then connect PCs to your switches so they become part of the network too. Amazing!!

So, if you haven't seen or used GNS3 yet I suggest you download it and give it a go, you won't be disappointed.