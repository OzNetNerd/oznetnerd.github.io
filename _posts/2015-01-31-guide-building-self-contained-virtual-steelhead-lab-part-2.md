---
title: 'Guide: Building a Self-Contained Virtual Steelhead Lab - Part 2'
sub_heading: "A series of posts to help you become a Steelhead expert!"
redirect_from: /guide-building-self-contained-virtual-steelhead-lab-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- Labs

tags:
- GNS3
- Labs
- Cisco
- VMWare
- Riverbed
---
**Note:** This post carries on from [**Part 1**](/guide-building-self-contained-virtual-steelhead-lab-part-1/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 1").

## VLANs

SW1 and SW2 have been used to allow multiple devices to connect to the VSH’s Primary, Auxiliary and lan0_0 subnets. You can either use trunk ports or access ports between the switches and R3 and R5. If using access ports, you’ll need a total of six ports per switch:

<table style="height: 548px;" width="661" data-blogger-escaped-style="border-collapse: collapse; border: none; mso-border-alt: solid windowtext .5pt; mso-padding-alt: 0cm 5.4pt 0cm 5.4pt; mso-yfti-tbllook: 1184;">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td colspan="4" width="543" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 407.3pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">SW1</strong></p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Switch Port #:</strong></td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VLAN:</strong></td>
<td style="text-align: center;" width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Connects to:</strong></td>
<td style="text-align: center;" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">1</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 2</td>
<td style="text-align: center;" width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">VSH-A Primary interface</td>
<td style="text-align: center;" rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">VSH-A Primary Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">2</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 2</td>
<td style="text-align: center;" width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">R3, Fa0/0</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">3</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 3</td>
<td style="text-align: center;" width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">VSH-A Auxiliary interface</td>
<td style="text-align: center;" rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">VSH-A Auxiliary Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">4</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 3</td>
<td style="text-align: center;" width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">R3, Fa0/1</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">5</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 4</td>
<td width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">VSH-A lan0_0 interface</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">VSH-A lan0_0 Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7; mso-yfti-lastrow: yes;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">6</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 4</td>
<td width="165" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 124.05pt;">
<p style="text-align: center;">R3, Fa1/0</p>
</td>
</tr>
</tbody>
</table>

<table style="height: 338px;" width="662" data-blogger-escaped-style="border-collapse: collapse; border: none; mso-border-alt: solid windowtext .5pt; mso-padding-alt: 0cm 5.4pt 0cm 5.4pt; mso-yfti-tbllook: 1184;">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td style="text-align: center;" colspan="4" width="547" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 410.25pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">SW2</strong></td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Switch Port #:</strong></td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VLAN:</strong></td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Connects to:</strong></td>
<td style="text-align: center;" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">1</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 5</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">VSH-B Primary interface</td>
<td style="text-align: center;" rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">VSH-B Primary Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">2</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 5</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">R5, Fa0/0</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">3</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 6</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">VSH-B Auxiliary interface</td>
<td style="text-align: center;" rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">VSH-B Auxiliary Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">4</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 6</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">R5, Fa0/1</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">5</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 7</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">VSH-B lan0_0 interface</td>
<td style="text-align: center;" rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">VSH-B lan0_0 Interface VLAN</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7; mso-yfti-lastrow: yes;">
<td style="text-align: center;" width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">6</td>
<td style="text-align: center;" width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">VLAN 7</td>
<td style="text-align: center;" width="170" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 127.4pt;">R5, Fa1/0</td>
</tr>
</tbody>
</table>
**Note:** The VLAN numbers align with the sub-segments mentioned previously.

## Final Diagram

Using the above IP addressing scheme and VLAN assignments, the topology looks like this:

[![netw1](/assets/2015/02/netw1.png)](/assets/2015/02/netw1.png)

## Lab Setup

Now that the background has been explained and the IP addressing as well as the VLAN mappings have been covered, it’s time to configure the lab.

### VMware Workstation
#### NIC Setup

This lab setup requires the use nine VMware Workstation NICs – VMnet0 through VMnet8. VMnet0 will be used to administer the ESXi host and VMnet1 through VMnet8 will be used to connect the VSH interfaces, GNS3 routers and Windows XP machines to one another.

To configure the NICs appropriately, you’ll need to do the following:

1. Click on “Edit” > “Virtual Network Editor…” then configure the Workstation NICs as follows:

*   Select “Host-only”Tick the “Connect a host virtual adapter to this network” box
*   Untick the “Use local DHCP service to distribute IP address to VMs” box
*   Configure the “Subnet IP” field of each NIC so that it aligns up with IP address plan discussed above:

<table style="height: 420px;" width="692" data-blogger-escaped-style="border-collapse: collapse; border: none; mso-border-alt: solid windowtext .5pt; mso-padding-alt: 0cm 5.4pt 0cm 5.4pt; mso-yfti-tbllook: 1184; width: 556px;">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td width="92" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VMnet NIC:</strong></td>
<td width="133" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">IP Address:</strong></td>
<td width="107" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Subnet Mask:</strong></td>
<td width="224" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet0</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.0.0.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">ESXi Host Network</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet1</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.1.2.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-A Primary network</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet2</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.1.3.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-A Auxiliary network</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet3</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.1.4.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-A in-path0_0 network (LAN)</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet4</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">&lt;Leave as default&gt;</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-A in-path0_0 network (WAN)</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet5</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.3.5.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-B Primary network</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet6</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.3.6.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-B Auxiliary network</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 8;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet7</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">10.3.7.0</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-B in-path0_0 network (LAN)</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 9; mso-yfti-lastrow: yes;">
<td width="92" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 69.2pt;">VMnet8</td>
<td width="133" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 99.5pt;">&lt;Leave as default&gt;</td>
<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
<td width="224" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 167.8pt;">VSH-B in-path0_0 network (WAN)</td>
</tr>
</tbody>
</table>

	**Note:** The description field is for your information only, you do not put it in to the NIC’s configuration.

	Once completed, your NICs should look like this:

	[![netw2](/assets/2015/02/netw2.png)](/assets/2015/02/netw2.png)

2. Click “OK”.

	After clicking “OK”, VMware will automatically create nine new NICs on your PC and assign each of them a .1 IP address in the subnet ranges you specified. You can view the NICs by navigating to “Control Panel” > “Network and Internet” > “Network Connections”:

	[![netw3](/assets/2015/02/netw3.png)](/assets/2015/02/netw3.png)
	
	**Note:** Default gateways are not configured on these NICs, nor are they needed.
	
	**Note:** The reason why VMnet4 and VMnet8’s “Subnet IP” setting were left as their default is because they must be in the same subnets as VMnet3 and VMnet7 respectively. (As mentioned previously, this is because the lan0_0 and wan0_0 interfaces are bridged, and therefore part of the same broadcast domain). However, Workstation does not allow you to put two VMnet NICs in the same subnet because if used incorrectly, it can create conflicts.

3. To work around this restriction, as mentioned above, leave the “Subnet IP” fields as their defaults. Now manually configure the NICs by navigating to “Network Connections” as described above. Once there, set the adapters the following IPs:

	*   VMware Network Adapter VMnet4 – 10.1.4.2 /24 – (no default gateway required)
	*   VMware Network Adapter VMnet8 – 10.3.7.2 /24 – (no default gateway required)
	
	To summarise, your NIC’s IPs should look like this:

<table style="height: 494px;" width="516">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td width="91" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VMnet NIC:</strong></td>
<td width="88" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">IP Address:</strong></td>
<td width="267" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Note:</strong></td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet0</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.0.0.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet1</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.2.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet2</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.3.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet3</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.4.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet4</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.4.2</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Manually configured by you</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet5</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.3.5.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet6</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.3.6.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 8;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet7</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.3.7.1</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Automatically configured by Workstation</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 9; mso-yfti-lastrow: yes;">
<td width="91" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 68.05pt;">VMnet8</td>
<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.3.7.2</td>
<td width="267" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 200.0pt;">Manually configured by you</td>
</tr>
</tbody>
</table>

	**Important:** After completing the above steps, you will need to reboot your PC. This is necessary in order to allow GNS3 to see the new NICs.

#### Windows XP VMs

Next, you’ll need to create a Windows XP VM. Once that VM is created, you’ll then need to clone it. This will result in you having two lab PCs.

1. Create one Windows XP VM named “VM-PC-1” and map it to the VMnet3 NIC.

    [![netw4](/assets/2015/02/netw4.png)](/assets/2015/02/netw4.png)

2. Install Windows XP.
3. When the installation is completed, disable the VM’s firewall and then shut down the machine.
4. Clone the newly created VM and name it “VM-PC-2”.

    **Note:** Cloning a VM avoids having to go through the Windows XP installation twice. You can choose either a Full Clone or a Linked Clone, it won’t matter. In the interest of conserving hard disk space though you should choose the Linked Clone option.

5. Map the cloned VM’s NIC to VMnet7.
6. Start both VMs and configure them VMs with their static IP addresses:

	<table style="height: 164px;" width="678" data-blogger-escaped-style="border-collapse: collapse; border: none; mso-border-alt: solid windowtext .5pt; mso-padding-alt: 0cm 5.4pt 0cm 5.4pt; mso-yfti-tbllook: 1184;">
	<tbody>
	<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
	<td width="74" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 55.8pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VM:</strong></td>
	<td width="88" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">IP Address:</strong></td>
	<td width="107" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Subnet Mask:</strong></td>
	<td width="125" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 94.05pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Default Gateway:</strong></td>
	<td width="181" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 135.4pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
	<td width="74" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 55.8pt;">VM-PC-1</td>
	<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.4.31</td>
	<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
	<td width="125" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 94.05pt;">10.1.4.11</td>
	<td width="181" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 135.4pt;">VSH-A in-path0_0 network</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 2; mso-yfti-lastrow: yes;">
	<td width="74" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 55.8pt;">VM-PC-2</td>
	<td width="88" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 66.0pt;">10.1.4.32</td>
	<td width="107" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 80.4pt;">255.255.255.0</td>
	<td width="125" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 94.05pt;">10.3.7.14</td>
	<td width="181" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 135.4pt;">VSH-B in-path0_0 network</td>
	</tr>
	</tbody>
	</table>

Doing this puts the two VMs on the same subnet as their respective VSHs, and uses R1 and R4 as the default gateways. (Refer to the “Final Diagram” above for more information).

**Note:** If you’d like to test connectivity, you should be able to send pings sourced from your PC and destined for the VMs and vice versa. If your pings fail in one or both directions, ensure that the firewalls are disabled on your VMs, and, if necessary, try temporarily disabling the firewall on your PC.

**Note:** You **will not** be able to ping between the two VMs at this stage. These pings will only be successful after the VSHs and GNS3 are set up.

#### ESXi VM

Now you will need to prepare your ESXi VM.

1.  Map your Workstation NICs (VMnet0 through VMnet8) to your ESXi VM. Once done, the VM should look like this:

	[![netw5](/assets/2015/02/netw5.png)](/assets/2015/02/netw5.png)

	**Note:** If you haven’t installed ESXi in a Workstation VM before, there are plenty of step by step tutorials available on the internet.

2. After adding the NICs, boot the VM and install ESXi.

### VMware ESXi

#### Management IP

1. Once you’ve finished installing ESXi, you will need to configure the server’s management IP address. On the console, navigate to:

	“Configure Management Network” > “Network Adapters” and select “vmnic0”.

	**Note:** In VMware Workstation, the NICs are known as “VMnet” adapters. In ESXi, they are known as “vmnics”. Although they are named differently, their numbering is the same – e.g VMnet0 in Workstation is mapped to vmnic0 in ESXi, and so on.

	[![netw6](/assets/2015/02/netw6.png)](/assets/2015/02/netw6.png)

2. Next, you’ll need to configure the server’s static management IP address, which is 10.0.0.251 /24.

	[![netw7](/assets/2015/02/netw7.png)](/assets/2015/02/netw7.png)
	
	**Note:** A default gateway is not required because, as mentioned previously, when Workstation installed your VMnet NICs it also put them in to the correct subnets. This means that your ESXi management IP and your “VMware Network Adapter VMnet0” interface will be on the same subnet.
	
	**Note:** An IPv6 management address is not required so it can be disabled.

#### vSphere Client

Next you’ll need to install the vSphere client software.

1. In a web browser, navigate to the ESXi host’s IP address (10.0.0.251) and then install the vSphere client by clicking on the “Download vSphere Client” link.

2. After you’ve installed the client, use it to log in to the ESXi server.

#### vSwitch Setup

Next you need to set up your vSwitches. By default, VMnet0 is connected to “vSwitch0” (which is called “vmware vmk”) while all other NICs are unassigned. You will need to create eight additional vSwitches and assign a single vmnic to each. To do this, complete the following steps:

1. Navigate to the “Networking” page, then click “Add Networking…” > “Virtual Machine” > “Create a vSphere standard switch”.

2. Assign the vmnics to the vSwitches and set the “Network Label” in accordance with the table below:

	<table style="height: 392px;" width="410">
	<tbody>
	<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
	<td width="75" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">vSwitch:</strong></td>
	<td width="65" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">vmnic:</strong></td>
	<td width="123" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Network Label:</strong></td>
	<td width="76" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VLAN ID:</strong></td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch1</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic1</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH A &ndash; Primary</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch2</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic2</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH A &ndash; Auxiliary</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch3</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic3</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH A &ndash; lan0_0</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch4</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic4</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH A &ndash; wan0_0</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch5</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic5</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH B &ndash; Primary</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch6</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic6</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH B &ndash; Auxiliary</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 7;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch7</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic7</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH B &ndash; lan0_0</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 8; mso-yfti-lastrow: yes;">
	<td width="75" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.4pt;">vSwitch8</td>
	<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.6pt;">vmnic8</td>
	<td width="123" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 92.3pt;">VSH B &ndash; wan0_0</td>
	<td width="76" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 56.75pt;">none</td>
	</tr>
	</tbody>
	</table>
	
	Next, you’ll need to configure “Promiscuous Mode” to “Accept” for the following vSwitches:
	
	*   vSwitch3 (VSH-A’s lan0_0 interface)
	*   vSwitch4 (VSH-A’s wan0_0 interface)
	*   vSwitch7 (VSH-B’s lan0_0 interface)
	*   vSwitch8 (VSH-B’s wan_0 interface)
	
	**Important:** This is a very important step. Not enabling “Promiscuous Mode” will result in the VSH being unable to intercept and optimise packets.

3. To enable Promiscuous mode, complete the following steps for each of the above mentioned vSwitches:
	1.  Click on “Properties” (to the right of the vSwitch name)
	2.  Click on the vSwitch’s name (e.g “VSH A – lan0_0”)
	3.  Click “Edit”
	4.  Click on the “Security” tab
	5.  Tick all three boxes and set them to “Accept”

See [**Part 3**](/guide-building-self-contained-virtual-steelhead-lab-part-3/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 3") to continue.