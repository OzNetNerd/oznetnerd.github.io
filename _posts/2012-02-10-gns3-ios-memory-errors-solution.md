---
title: GNS3 IOS Memory Errors - Solution
sub_heading: "Another day, another memory issue!"
redirect_from: /gns3-ios-memory-errors-solution/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs

tags:
- GNS3
- Cisco
- Lab
---
[**In a previous post**](/gns3-ios-memory-errors/ "GNS3 IOS Memory Errors") I talked about GNS3 memory errors that produced the below log messages when certain IOS images were used and NATing was enabled:

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

Then, the following message would be displayed in the GNS3 Console:

```
=> *** Warning: ghostsize is to small for device R1. Increase it with the ghostsize option.
```

Well, thanks to an anonymous commenter, we now have a fix to the issue. Here is what they wrote:

_I had this same thing. Here's how I fixed it quickly. Instead of upping RAM manually on all device, edit the .net file for your project and set the 'ram' parameter on the model as follows:_

```
{% raw %}-------------------{% endraw %}
[127.0.0.1:7200]
workingdir = /tmp
udp = 10000
[[3745]]
image = /downloads/c3745-advipservicesk9-mz.124-15.T14.bin
ram = 256
idlepc = 0x60bc1748
ghostios = True
{% raw %}-------------------{% endraw %}
```

_Now all devices you create from that model will have the same ram setting. seems to have cleared it up the issue for me anyway..._

Looking at the other comments that were posted after the above one, it seems to be resolving the issues for others too.

Thanks to the anonymous commenter and to the others for letting us all know that it worked for you too! :)