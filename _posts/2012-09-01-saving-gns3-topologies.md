---
title: Saving GNS3 Topologies
sub_heading: "Make sure you don't lose your labs"
redirect_from: /saving-gns3-topologies/

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
A lot of people (myself included) have had trouble saving their GNS3 topologies properly. You think you've done it properly, only to find the next time you open the topology, some or all of the devices have lost their configurations. This can be very frustrating, especially if it was a large and complex network.

The solution I found, which is most probably overkill (but it works every time so I'm happy), is to do the following:

1.  When you open GNS3, a popup window will appear saying **"New Project"**. Make sure the **"Save IOS startup configuration"** box is ticked, then, choose a location where you'd like to store the project files.Once you have done that, you are free to create and configure your GNS3 topology.
2.  Once your done, issue the **"copy run start"** command on every router.
3.  While the the topology is still running, click **"File"**, then click **"Save"**.
4.  Again, while the topology is still running, close GNS3 (either by clicking the **"X"** at the top right hand corner of the screen, or by going to **"File"**, then **"Quit"**. GNS3 will then say **"Would you like to save the current topology?"** - Click **"Yes"**.

And that's it! If you follow this procedure you will find that your topology, and your configurations, are successfully saved every time.

When you use GNS3 to open a previously saved topology, follow steps 2 through 4 above to ensure any changes you make to the topology and/or router configurations are saved properly.

### Changing Names

After performing the above steps, you will find that GNS3 will create ".cfg" files in your project's directory. Each ".cfg" file will map to each device in your topology. For example, if you have **R1**, **R2** and **R3**, in your topology, the ".cfg" files will be **R1.cfg**, **R2.cfg** and **R3.cfg**.

Because of this, a problem will occur if you decide to change the routers' names in the GNS3 topology window. For example, if you change the above mentioned routers to Spoke, Internet and Core, these routers will now look for Spoke.cfg, Internet.cfg and Core.cfg. if they're not found, GNS3 will create blank copies. So, instead of keeping their original configuration, each device will have no configuration at all.

A simple fix would be to go in to your project directory and rename each of the old ".cfg" files to their new device names.

For example:

*   Delete the blank **Spoke.cfg**, **Internet.cfg** and **Core.cfg** files.
*   Rename **R1.cfg** to **Spoke.cfg**
*   Rename **R2.cfg** to **Internet.cfg**
*   Rename **R3.cfg** to **Core.cfg**

Once you have done that, restart your topology and all will be well again :)

**Note:** Just to reiterate, the name changes above refer to GNS3's topology window, not the Cisco "hostname" command. If you change the router's name by using the "hostname" command, you **do not** need to make any of the above mentioned changes.