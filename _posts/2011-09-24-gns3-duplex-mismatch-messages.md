---
title: GNS3 Duplex Mismatch Messages
sub_heading: "Fixing GNS3 duplex errors"
redirect_from: /gns3-duplex-mismatch-messages/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Misc.

tags:
- GNS3
- Labs
- Cisco
- Useful Commands
---
When Cisco devices are connected to one another and CDP is enabled (which it is by default), if one port is configured as full duplex but the other is configured as half duplex, the two devices will log "duplex mismatch" messages.

This can be very helpful in the real world. However, when using GNS3 these messages can appear for no reason at all, and they will constantly reappear, over and over again. Things get worse when you've got one router connected to two others, as was the case in the example below:

```
01:43:20.579: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/0 (not half duplex), with R1 FastEthernet0/0 (half duplex).
01:43:20.911: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/1 (not half duplex), with R2 FastEthernet0/0 (half duplex).
01:44:20.839: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/1 (not half duplex), with R2 FastEthernet0/0 (half duplex).
01:45:20.567: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/0 (not half duplex), with R1 FastEthernet0/0 (half duplex).
01:45:20.971: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/1 (not half duplex), with R2 FastEthernet0/0 (half duplex).
01:46:20.607: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/0 (not half duplex), with R1 FastEthernet0/0 (half duplex).
01:46:20.935: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/1 (not half duplex), with R2 FastEthernet0/0 (half duplex).
01:47:20.579: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/0 (not half duplex), with R1 FastEthernet0/0 (half duplex).
01:47:20.983: %CDP-4-DUPLEX_MISMATCH: duplex mismatch discovered on FastEthernet1/1 (not half duplex), with R2 FastEthernet0/0 (half duplex).
```

As the duplex mismatch is found through CDP, you could always disable CDP on all of your GNS3 routers to stop these messages appear. However, this is not may not be an ideal solution for you, especially if you've got a large lab network. The other alternative is to issue the following command:

```
R3(config)#no cdp log mismatch duplex
```

This command stops the log messages appearing, while still leaving CDP enabled.