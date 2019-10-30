---
title: GNS3 IOS Memory Errors
sub_heading: "Fixing GNS3 memory errors"
redirect_from: /gns3-ios-memory-errors/

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
**Update:** Please see [**this page**](/gns3-ios-memory-errors-solution/ "GNS3 IOS Memory Errors – Solution") for a fix to this issue.

The other day I decided to upgrade my GNS3 IOS to c3725-adventerprisek9-mz.124-15.T14.bin. All seemed to be going well until I tried to issued the "ip nat inside" command on one of the interfaces...

```
R1(config)#int fa0/0
R1(config-if)#ip nat inside
% NBAR ERROR: parsing stopped
% NBAR Error : Activation failed due to insufficient dynamic memory
% NBAR Error: Stile could not add protocol node
%NAT: Error activating CNBAR on the interface FastEthernet0/0
*Mar 1 00:00:27.307: %SYS-2-MALLOCFAIL: Memory allocation of 10260 bytes failed from 0x62912920, alignment 0
Pool: Processor Free: 13696 Cause: Memory fragmentation
Alternate Pool: None Free: 0 Cause: No Alternate pool
 -Process= "Exec", ipl= 0, pid= 94, -Traceback= 0x61488C44 0x60015E58 0x6001BDB8 0x6001C410 0x636726CC 0x62912928 0x628F12D8 0x628F6E7C 0x628F25B4 0x628F7104 0x628F25B4 0x628F257C 0x628F4F90 0x628F25B4 0x628F2778 0x62925C0
*Mar 1 00:00:27.311: %NBAR-2-NOMEMORY: No memory available for StILE lmalloc, -Traceback= 0x61488C44 0x62912944 0x628F12D8 0x628F6E7C 0x628F25B4 0x628F7104 0x628F25B4 0x628F257C 0x628F4F90 0x628F25B4 0x628F2778 0x62925C08 0x6293066C 0x6291D81C 0x6293ABBC 0x6293AF3C
R1(config-if)#
*Mar 1 00:00:27.863: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, changed state to up
*Mar 1 00:00:30.263: %AAA-3-ACCT_LOW_MEM_UID_FAIL: AAA unable to create UID for incoming calls due to insufficient processor memory
```

Things got worse when I tried issued the **"ip nat outside"** command on the other interface...

```
R1(config-if)#int fa0/1
R1(config-if)#ip nat outside
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR ERROR: symbol addition
% NBAR Error : Activation failed due to insufficient dynamic memory
% NBAR Error: Stile could not add protocol node
%NAT: Error activating CNBAR on the interface FastEthernet0/1
```

Then the router complained some more when I issued the **"ip nat inside source"** command...

```
R1(config-if)#ip nat inside source list TEST int fa0/0
R1(config)#
*Mar 1 00:01:28.651: %SYS-2-MALLOCFAIL: Memory allocation of 65536 bytes failed from 0x625B21C8, alignment 0
Pool: Processor Free: 8836 Cause: Not enough free memory
Alternate Pool: None Free: 0 Cause: No Alternate pool
 -Process= "Exec", ipl= 0, pid= 94, -Traceback= 0x61488C44 0x60015E58 0x6001BDB8 0x6001C2C4 0x625B21D0 0x625B3818 0x6254A408 0x62509078 0x625C223C 0x6255B8E0 0x6257DAE8 0x61CDB278 0x61CDB518 0x61C70178 0x61C7596C 0x614D5078
*Mar 1 00:01:28.671: CCE_CP: Can't cce_create_class_group: Can't allocate class group id.
```

After seeing all this, I got to work trying to resolve the issue. As the log messages clearly say, there is insufficient memory. So I thought I'd just upgrade the memory allocated to the router and all should be well. Unfortunately, all was not well. After increasing the memory and rebooting the router, I received the following error message in the GNS3 Console:

```
=> *** Warning: ghostsize is to small for device R1. Increase it with the ghostsize option.
```

I can only assume that this "ghostsize" option relates to Ghostios, which, according to the GNS3 Quick Start guide is used to:

_"Significantly reduce the amount of real host RAM needed for labs with multiple routers running the same IOS image. With this feature, instead of each virtual router storing an identical copy of IOS in its virtual RAM, the host will allocate one shared region of memory that they will all utilize. So for example, if you are running 10 routers all with the same IOS image, and that image is 60 MB in size you will save 9*60 = 540 MB of real RAM when running your lab. Ghostios is enabled, by default, in GNS3."_

A very useful application indeed. However, after an hour or so of searching, I was unable to find any information about the ghostsize option or its syntax, so I had no other option but to return to using my previous IOS, **c3725-advipservicesk9-mz.124-4.T8.bin**. I know it is old, but it has been reliable and up to this date, has supported all of the features I have needed, so I'm happy to continue using it.

### **UPDATE**

Please see [**this page**](/gns3-ios-memory-errors-solution/ "GNS3 IOS Memory Errors – Solution") for a fix to this issue.