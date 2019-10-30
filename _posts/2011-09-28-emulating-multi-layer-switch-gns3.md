---
title: Emulating a Multi Layer Switch in GNS3
sub_heading: "The closest thing to switching you can get in GNS3"
redirect_from: /emulating-multi-layer-switch-gns3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- EIGRP

tags:
- Labs
- Cisco
- EIGRP
- Routing Protocols
---
As most of you know, you can get switch-like capabilities in GNS3 by inserting a NM-16ESW in to a compatible router. This is necessary because at present, dynamips is unable to emulate a real switch. This is because Cisco switches use hardware ASICs to perform their duties and unfortunately it is difficult/impossible for this to be emulated in software.

This is not so bad though as you may find you can get quite a lot done with an NM-16ESW. You can even use it to emulate a multilayer switch, as I will now demonstrate using the following topology:

[![topology](/assets/2015/02/topology8.jpg)](/assets/2015/02/topology8.jpg)

As you can see by the interface numbers on **R1** (**f1/0**, **f1/1** and **f1/2**), I'm only using the router's NM-16ESW module, not its integrated layer 3 ports.

Using the **"no switchport"** command on **R1's fa1/0** port will turn it in to a layer 3 interface, and therefore will allow me to assign an IP address to it:

```
R1(config)#interface FastEthernet1/0
R1(config-if)# no switchport
R1(config-if)# ip address 10.1.1.1 255.255.255.252
```

Next, I'll configure VLAN10 and 20. (Unfortunately the NM-16ESW requires you use the older **"VLAN database"** command as opposed to the newer **"vlan"** command that multilayer switches use):

```
R1#vlan database
R1(vlan)#vlan 10
R1(vlan)#vlan 20
R1(vlan)#apply
R1(vlan)#exit
```

Now that the VLAN interfaces are created, I can now assign IP addresses to them:

```
R1(config)#interface Vlan10
R1(config-if)# ip address 10.10.10.1 255.255.255.252
R1(config-if)#interface Vlan20
R1(config-if)# ip address 10.20.20.1 255.255.255.252
```

I can also assign ports to the VLANs too. As per the diagram, port **Fa1/1** is to be put in **VLAN10** and port **Fa1/2** is to be put in **VLAN20**:

```
R1(config-if)#interface FastEthernet1/1
R1(config-if)# switchport access vlan 10
R1(config-if)#interface FastEthernet1/2
R1(config-if)# switchport access vlan 20
```

And that's it for **R1** at this stage.

In regards the **VLAN10**, **VLAN20** and **R2** routers, they all have very simple configurations, as shown below:

**VLAN10:**

```
interface FastEthernet0/0
 ip address 10.10.10.2 255.255.255.252
!
ip route 0.0.0.0 0.0.0.0 10.10.10.1
```

**VLAN20:**

```
interface FastEthernet0/0
 ip address 10.20.20.2 255.255.255.252
!
ip route 0.0.0.0 0.0.0.0 10.20.20.1
```

**R2:**

```
interface FastEthernet0/0
 ip address 10.1.1.2 255.255.255.252
!
ip route 0.0.0.0 0.0.0.0 10.1.1.1
```

One other thing to note is that as the connection between **R1** and **R2** is layer 3, you could remove the default route from **R2's** configuration and use a routing protocol such as EIGRP to advertise the accessible routes instead:

**R1:**

```
router eigrp 10
 network 10.1.1.1 0.0.0.0
 network 10.10.10.1 0.0.0.0
 network 10.20.20.1 0.0.0.0
 no auto-summary
```

**R2:**

```
router eigrp 10
 network 10.1.1.2 0.0.0.0
 no auto-summary
!
no ip route 0.0.0.0 0.0.0.0 10.1.1.1
```

After performing the above changes, the routing table will look like this:

```
R2(config-router)#do sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
 D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
 N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
 E1 - OSPF external type 1, E2 - OSPF external type 2
 i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
 ia - IS-IS inter area, * - candidate default, U - per-user static route
 o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

10.0.0.0/30 is subnetted, 3 subnets
D 10.20.20.0 [90/284160] via 10.1.1.1, 00:34:06, FastEthernet0/0
D 10.10.10.0 [90/284160] via 10.1.1.1, 00:36:41, FastEthernet0/0
C 10.1.1.0 is directly connected, FastEthernet0/0
```