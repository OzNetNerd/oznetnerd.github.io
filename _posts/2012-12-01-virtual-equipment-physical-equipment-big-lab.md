---
title: Virtual Equipment + Physical Equipment = Big Lab
sub_heading: "When it comes to labs, bigger is always better!"
redirect_from: /virtual-equipment-physical-equipment-big-lab/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Misc.
- Data Center

tags:
- GNS3
- Labs
- Cisco
- Nexus
- Data Center
- N1KV
- VMWare
---
I have posted a few entries covering [**GNS3**](/tags/#GNS3) and how you can use it to help you with your studies. And, in the [**Connecting Your PC to Your Virtual GNS3 Routers**](/connecting-pc-virtual-gns3-routers/ "Connecting Your PC to Your Virtual GNS3 Routers") I showed you how it was possible to break your GNS3 routers out of the Virtual world and bring them in to the Physical world. In this post, I am to show you how I used this technique in my lab to give me a rather nice setup.

**Figure 1** shows the equipment I own at the time of writing. I don't really use the 2610 as GNS3 fulfills all of my router needs. I do use the 2509 though as a console router for the rest of my equipment.

**Figure 1**

[![fig1](/assets/2015/02/fig1.png)](/assets/2015/02/fig1.png)You may be wondering why I need four physical machines... well, at least the wife does ;) My explanation can be found in **Figure 2**.

Each physical machine has a Virtual Machine (VM) installed. These VMs are bound to their own physical NICs. This is why each machine has a minimum of three NICs. The reason why one machine has five NICs is because it also hosts GNS3. Two of its NICs are assigned to the job of connecting virtual routers to the physical network.

**Figure 2**

[![fig2](/assets/2015/02/fig2.png)](/assets/2015/02/fig2.png)

**Figure 3** shows how each of the physical NICs are mapped.

**Figure 3**

[![fig3](/assets/2015/02/fig3.png)](/assets/2015/02/fig3.png)

**Figure 4** tries to explain (or should I say justify?) why I have five monitors :)

**Figure 4**

[![fig4](/assets/2015/02/fig4.png)](/assets/2015/02/fig4.png)

Finally, **Figure 5** shows a basic topology which can be created with the above equipment.

**Figure 5**

[![fig5](/assets/2015/02/fig5.png)](/assets/2015/02/fig5.png)

For a more detailed, step by step guide on how to achieve the above, please see my [**Ultimate Cisco Lab**](http://www.ultimateciscolab.info) website.

## Update: Where to from here?

My lab has increased in size quite a lot since this post was first written (see the photos below). Even more exciting though is the [**purchase of my new PC**](/new-pc-nexus-1000v-come/ "New PC – Nexus 1000V here I come!"). With this PC I will be able to set up a few ESXi hosts, [**Nexus 1000V**](/download-install-nexus-1000v-free/ "Download & Install Nexus 1000v For Free!") VSMs and VEMs. This will enable me to play around with vCenter, vMotion, VSM High Availability, etc. This, in conjunction with the additional VMs I will be able to create inside the ESXi hosts, will allow me to create even larger topologies than before. I can't wait to get started! :)

The lab as it stands now:

[![lab1](/assets/2015/02/lab1.jpg)](/assets/2015/02/lab1.jpg)

[![lab2](/assets/2015/02/lab2.jpg)](/assets/2015/02/lab2.jpg)