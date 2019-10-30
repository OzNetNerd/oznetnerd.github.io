---
title: Finding a Host's Switchport
sub_heading: "Marco... Polo!!"
redirect_from: /finding-a-hosts-switchport/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Cisco
- Misc.
tags:
- Labs
- Cisco
- Useful Commands
---
In this post I will demonstrate how we can find out which of **SW3's** switchports **PC1** is connected to in the topology diagram below. To make things more fun though I'll begin my search from **R1**.

Note that apart from **R1** and **PC1's** IP addresses, we do not have nor need any other information such as intermediate device IPs or port numbers in order to get started. Also note that the diagram is only used to show you, the reader what the topology looks like. As explained below, when doing this in a real topology you do not need a topology diagram to be able to successfully locate the host's corresponding switchport.

[![top1](/assets/2015/10/top1.png)](/assets/2015/10/top1.png)

Let's get started. As you probably expected, the first thing we need to do is perform a traceroute to **PC1**:

```
R1#tracer 192.168.100.147

Type escape sequence to abort.
Tracing the route to 192.168.100.147

  1 10.1.2.2 16 msec 8 msec 8 msec
  2 10.2.3.3 20 msec 20 msec 20 msec
  3 10.3.4.4 32 msec 16 msec 44 msec
  4 10.4.10.10 56 msec 40 msec 36 msec
  5  *  *  *
  6  *  *  *
```

This tells us that the closest we can get to **PC1** is a device with the IP address **10.4.10.10**. It also tells us that unless our traffic is being blocked this device is the last Layer 3 hop between **R1** and **PC1**. In this case we know that there are no security policies in the network, therefore the latter is true.

Let's jump onto this device and see what we can find.

```
R1#telnet 10.4.10.10
Trying 10.4.10.10 ... Open


User Access Verification

Username: cisco
Password:
SW1#
```

Given the prompt above we now know that **10.4.10.10** belongs to **SW1**.

As mentioned above we know that **SW1** is the last Layer 3 hop between **R1** and **PC1**. The reason why I point this out is because it means that our tracing needs to move from Layer 3 (IP) to Layer 2 (MAC). In order to do this we'll need to do an ARP  lookup on **PC1's** IP address:

```
SW1#sh ip arp 192.168.100.147
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  192.168.100.147       246   c208.09bc.0000  ARPA   Vlan1
```

Great, now that we know that **PC1's** MAC address is **c208.09bc.0000** we're now able to continue our tracing at Layer 2. In order to do this we'll need to look up **SW1's** MAC table:

```
SW1#sh mac-add add c208.09bc.0000
Destination Address  Address Type  VLAN  Destination Port
-------------------  ------------  ----  --------------------
c208.09bc.0000          Dynamic       1     FastEthernet1/7
```

The above tells us that **PC1's** MAC address resides on **FastEthernet1/7**. However, not so fast! Let's take a look at what's actually on this port:

```
SW1#sh cdp ne fa1/7 de
-------------------------
Device ID: SW2
Entry address(es):
  IP address: 192.168.100.2
Platform: Cisco 3725,  Capabilities: Router Switch IGMP
Interface: FastEthernet1/7,  Port ID (outgoing port): FastEthernet1/5
Holdtime : 178 sec

Version :
Cisco IOS Software, 3700 Software (C3725-ADVIPSERVICESK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2010 by Cisco Systems, Inc.
Compiled Tue 17-Aug-10 12:08 by prod_rel_team

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 1
Duplex: full
```

OK so it turns that **SW2**, not **PC1,** is connected to **SW1's** **FastEthernet1/7**. Let's now jump on **SW2** and let's look for PC1's MAC address again:

```
SW2#telnet 192.168.100.2
Trying 192.168.100.2 ... Open


User Access Verification

Username: cisco
Password:
SW2#sh mac-add add c208.09bc.0000
Destination Address  Address Type  VLAN  Destination Port
-------------------  ------------  ----  --------------------
c208.09bc.0000          Dynamic       1     FastEthernet1/14

SW2#sh cdp ne fa1/14 de
-------------------------
Device ID: SW3
Entry address(es):
  IP address: 192.168.100.3
Platform: Cisco 3725,  Capabilities: Router Switch IGMP
Interface: FastEthernet1/14,  Port ID (outgoing port): FastEthernet1/3
Holdtime : 144 sec

Version :
Cisco IOS Software, 3700 Software (C3725-ADVIPSERVICESK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2010 by Cisco Systems, Inc.
Compiled Tue 17-Aug-10 12:08 by prod_rel_team

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 1
Duplex: full
```

Let's break down the latest otuput:

1.  PC1's MAC address was found on **SW2's FastEthernet1/14** interface.
2.  This port connects to another switch called **SW3**.
3.  The IP address of this switch is 192.168.100.3

Let's now repeat the above process one more time:

```
SW2#telnet 192.168.100.3
Trying 192.168.100.3 ... Open


User Access Verification

Username: cisco
Password:
SW3#sh mac-add add c208.09bc.0000
Destination Address  Address Type  VLAN  Destination Port
-------------------  ------------  ----  --------------------
c208.09bc.0000          Dynamic       1     FastEthernet1/10

SW3#sh cdp ne fa1/10
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
PC1              Fas 1/10           170         R S I     3725      Fas 0/0
```

We've done it! As per the above output we can see that **PC1** is connected to **SW3's FastEthernet1/10** interface. However, in the real world more often than not your servers and PCs won't be running CDP so this output is a bit of a cheat. So how would you confirm this is the correct port in a real world situation? Well, there are a few ways:

1.  **Check the port description** - Does it match the details of the server/PC that you're looking for?
2.  **Check the port configuration** - If you're looking for a PC it is more than likely the port will be configured as an "access" port in a user VLAN.
3.  **Check the MAC address(es)** - Is there more than one MAC address on this port? If yes, is that expected? If you're only expecting to see a single MAC address but you see multiple addresses it is likely there is a switch connected to this port.
4.  **Trace the cable** - If all else fails, physically trace the network cable to see what it connects to.