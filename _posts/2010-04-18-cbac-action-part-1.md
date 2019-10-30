---
title: CBAC in Action, Part 1
sub_heading: "Let's take CBAC for a spin"
redirect_from: /cbac-action-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Security

tags:
- CBAC
- Firewall
- NBAR
- Security
- GNS3
- Labs
- Cisco
---
In a **[previous post](/cbac-firewall/)** I talked about [**CBAC**](/tags/#cbac) and a few of the ways in which it, in conjunction with **[NBAR](/tags/#nbar)** can be used to secure your network. Today I will create a lab to show you how to put it to good use.

In this lab we have four routers, **R1**, **R2**, **R3** and **R4** (very original I know). Here are there designations:

*   **R1** = Local LAN - Your network
*   **R2** = Core - The core of your network and your internet connection
*   **R3** = Ping Destination - A location you want to ping to
*   **R4** = Hacker - A hacker that is trying to access your network

[![topology](/assets/2015/02/a04.jpg)](/assets/2015/02/a04.jpg)

Now in order to protect the network we need to apply access lists and CBAC rules to the interfaces which are connected to untrusted networks. As per the diagram, this would be interface **Fa0/0** and **Fa1/0**.

At present there are no security measures in place, so as per the example below, **R3** (the ping destination) can successfully ping **R2**:

```
R3#ping 192.168.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/57/100 ms
R3#
```

And, **R4** (the hacker) can also ping **R2**:

```
R4#ping 192.168.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 416/469/528 ms
R4#
```

And of course, **R2** can ping both **R3** and **R4**.

Now, to stop these external users (**R3** and **R4**) from being able to communicate with **R2**, we will need to put inbound ACLs on **R1's** interfaces which connect to them - (**Fa0/0** and **Fa1/0**). An ACL that denies all traffic will do the trick:

```
!!Create the ACL
ip access-list extended DENY_IN
deny ip any any
!
!!Apply it to the interface connected to R3
interface FastEthernet0/0
ip address 10.10.10.1 255.255.255.0
ip access-group DENY_IN in
!
!!Apply it to the interface connected to R4
interface FastEthernet1/0
ip address 10.50.50.1 255.255.255.0
ip access-group DENY_IN in
```

As per the below output, **R4** is now unable to ping to **R2**:

```
R4#ping 192.168.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.2, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
R4#
```
However, this also means that if **R2** tries to ping **R3** or **R4**, the ping will be unsuccessful because the returning traffic will be blocked by the incoming ACL:

```
R2#ping 10.10.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.10.2, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
R2#
```

This is obviously not ideal. To resolve this issue we need to create a CBAC rule which will watch our outgoing traffic and then create dynamic ACL entries at the top of our existing ACL to allow the return traffic to get back in to the network.

The following commands need to be entered on **R1**:

```
!!Create the CBAC rule to keep track of ICMP traffic
ip inspect name CBAC_OUT icmp
!
!!Apply it to the interface connected to R3
interface FastEthernet0/0
ip address 10.10.10.1 255.255.255.0
ip access-group DENY_IN in
ip inspect CBAC_OUT out
!
!!Apply it to the interface connected to R4
interface FastEthernet1/0
ip address 10.50.50.1 255.255.255.0
ip access-group DENY_IN in
ip inspect CBAC_OUT out
```

As per above, **R3** and **R4** are still unable to ping to **R2**:

```
R4#ping 192.168.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.2, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
R4#
```

But we can now ping to them from **R2**:

```
R2#ping 10.50.50.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.50.50.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 68/121/192 ms
R2#
```

To view the temporary ACL entries that CBAC creates, perform a ping from **R2** to **R3** and then issue the _"show ip inspect sessions"_ command on **R1**:

```
R1#show ip inspect sessions
Established Sessions
Session 664FD9A4 (192.168.0.2:8)=>(10.10.10.2:0) icmp SIS_OPEN
```

As you can see by the output of the above command, only ICMP traffic destined for the IP address that issued the ping command is allowed back in to the network, no other traffic. And, once the pings stop the temporary ACL entry is removed.

```
R1#sh ip inspect sessions
R1#
```

We have now successfully secured the network. External users are unable to send data to us unless we request that they do. In this example it was a ping reply but it can also be returning traffic from a HTTP server, FTP server, telnet session, SSH session, SIP call, etc.

For more information on CBAC or to see a more advanced CBAC configuration, please see my [**CBAC in Action, Part 2**](/cbac-action-part-2/) post.