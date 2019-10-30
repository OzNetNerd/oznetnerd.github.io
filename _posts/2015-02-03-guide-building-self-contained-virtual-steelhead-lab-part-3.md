---
title: 'Guide: Building a Self-Contained Virtual Steelhead Lab - Part 3'
sub_heading: "A series of posts to help you become a Steelhead expert!"
redirect_from: /guide-building-self-contained-virtual-steelhead-lab-part-3/

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
**Note:** This post carries on from [**Part 2**](/guide-building-self-contained-virtual-steelhead-lab-part-2/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 2").

## Virtual Steelhead
### Installation

Now that the vSwitches are set up, the next thing you will need to do is install the VSHs.

1. Through the vSphere client, install the VSH by clicking on “File” > “Deploy OVF Template”.
2. When asked for the name of the VM, type “VSH-A”.
3. When configuring the NICs, match the “Source Networks” to their appropriate “Destination Networks”, as per the image below.

    [![netw1](/assets/2015/02/netw11.png)](/assets/2015/02/netw11.png)

4. Power on the VM and open a vSphere client console session to it.
5. Log in to the appliance using “admin” and “password” as the credentials.
6. You will then be presented with the initial configuration wizard. See the responses to the wizard’s questions in the image below:

    [![netw2](/assets/2015/02/netw21.png)](/assets/2015/02/netw21.png)

7. Press enter twice to save the configuration.

    **Note:** You can now access VSH-A through your web browser by browsing to http://10.1.2.252 – and, once you’ve completed Step 8, you can access VSH-B by browsing to http://10.3.5.252

8. Repeat steps 1 through 7 but this time use VSH-B’s details while deploying the VM:

    *   Name the VM “VSH-B”
    *   Use the “VSH-B” destination networks

    Also, use VSH-B’s details while completing the initial configuration wizard:

	<table>
	<tbody>
	<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
	<td width="218" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step:</td>
	<td width="85" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">Setting:</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
	<td width="218" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step 1: Hostname</td>
	<td width="85" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">VSH-B</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
	<td width="218" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step 3: Primary IP address</td>
	<td width="85" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">10.3.5.252</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
	<td width="218" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step 5: Default gateway</td>
	<td width="85" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">10.3.5.15</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
	<td width="218" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step 14: In-Path IP</td>
	<td width="85" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">10.3.7.254</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 5; mso-yfti-lastrow: yes;">
	<td width="218" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 163.25pt;">Step 16: In-Path Default gateway</td>
	<td width="85" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 63.65pt;">10.3.7.14</td>
	</tr>
	</tbody>
	</table>

    **Note:** Refer to the “Final Diagram” diagram in [**Part 1**](guide-building-self-contained-virtual-steelhead-lab-part-1/) for more information about the interfaces and their IP addresses.

    **Note:** If you make a mistake while completing the wizard, you can re-run it by issuing the following commands:

```
enable
configuration terminal
configuration jump-start
```

## Licencing

Once you’ve completed the VSH installs and have logged in to them via a web browser, you will see an alert which states that optimisation is not properly licenced. To resolve this issue, complete the following steps on both VSHs:

1. Obtain a couple of tokens from Riverbed.
2. On your VSHs, navigate to the “Licenses” page and paste one of the tokens in to the “License Request Token” field, then click “Generate License Request Key”.
3. The page will reload and you will be given a “license request key”. Copy and paste it in to Riverbed’s licences page.
4. After completing the previous step, you will be presented with a list of licences. Click on the “View Keys - text” link which is located near the top of the page, then copy all of the licences which are at the bottom of the page.
5. Go back to your VSH’s licences page and click the “Add a New License” button. Paste all of the licences in to the box and then click “Add”.
6. You must now restart the Optimisation Service. You do this by navigating to “Configure” > ”Maintenance” > “Services”, and clicking the “Restart” button.
7. Refresh the page and the licencing alert should no longer appear. The appliance’s status will also change from “Critical” to “Healthy”.
8. Click the “Save” at the top of the page.

## GNS3

To tie everything together, and to add routing to your lab topology, you’ll now need to set up **[GNS3](/tags/#GNS3)**.

### VLAN Configuration

As mentioned earlier, you can configure SW1 and SW2 to allow multiple devices to connect to the VSH’s Primary, Auxiliary and lan0_0 subnets. To do this, use the following mappings:

<table style="height: 592px;" width="725">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td colspan="4" width="608" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 456.35pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">SW1</strong></p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Switch Port #:</strong></p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VLAN:</strong></p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Connects to:</strong></p>
</td>
<td width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">1</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 2</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">VMnet1 (VSH-A Primary interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">
<p style="text-align: center;">VSH-A Primary Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">2</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 2</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">R3, Fa0/0</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">3</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 3</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">VMnet2 (VSH-A Auxiliary interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">
<p style="text-align: center;">VSH-A Auxiliary Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">4</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 3</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">R3, Fa0/1</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">5</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 4</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">VMnet3 (VSH-A lan0_0 interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.85pt;">
<p style="text-align: center;">VSH-A lan0_0 Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7; mso-yfti-lastrow: yes;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">6</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 4</p>
</td>
<td width="231" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 173.1pt;">
<p style="text-align: center;">R3, Fa1/0</p>
</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<table style="height: 594px;" width="723">
<tbody>
<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
<td colspan="4" width="607" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 455.55pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">SW2</strong></p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Switch Port #:</strong></p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VLAN:</strong></p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Connects to:</strong></p>
</td>
<td width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">
<p style="text-align: center;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Description:</strong></p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">1</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 5</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">VMnet5 (VSH-B Primary interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">
<p style="text-align: center;">VSH-B Primary Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">2</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 5</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">R5, Fa0/0</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">3</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 6</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">VMnet6 (VSH-B Auxiliary interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">
<p style="text-align: center;">VSH-B Auxiliary Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">4</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 6</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">R5, Fa0/1</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">5</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 7</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">VMnet7 (VSH-B lan0_0 interface)</p>
</td>
<td rowspan="2" width="206" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 154.45pt;">
<p style="text-align: center;">VSH-B lan0_0 Interface VLAN</p>
</td>
</tr>
<tr data-blogger-escaped-style="mso-yfti-irow: 7; mso-yfti-lastrow: yes;">
<td width="106" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 79.7pt;">
<p style="text-align: center;">6</p>
</td>
<td width="65" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 48.7pt;">
<p style="text-align: center;">VLAN 7</p>
</td>
<td width="230" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 172.7pt;">
<p style="text-align: center;">R5, Fa1/0</p>
</td>
</tr>
</tbody>
</table>

However, before you can map the VSHs’ interfaces to GNS3 (the VMnet interfaces in the above tables), first you must use GNS3 “Cloud” nodes to represent the VSHs and attach the VMnet interfaces to them.

**Note:** You can use this method to connect any equipment (whether it be physical or virtual) to GNS3, not just Virtual Steelheads.

### Cloud Nodes

When you want to connect GNS3 routers to equipment which resides outside of GNS3, you need to use Cloud nodes. These nodes allow you to bind both physical and virtual NICs to your GNS3 topology. You then connect these NICs to your equipment and you’ll then be able to pass traffic between GNS3 and your equipment.

You can then change the cloud’s icon to something that more closely relates to the equipment you’re using. In the case of the GNS3 topology discussed in this guide, the icons for VM-PC-1 and VM-PC-2 (the Windows XP VMs) were changed from clouds to servers, and the VSH’s icons were changed from clouds to firewalls (seeing as though there is no Steelhead” icon).

Note: The icons for PC-A-01, PC-A-02 and PC-B-01 (which are GNS3 routers, not clouds) were changed to PCs because they’re being used to perform similar jobs to the test PCs.

To connect the VSHs’ interfaces to GNS3, complete the following steps:

1. Drag two GNS3 switches in to the topology pane and configure their interfaces and VLAN assignments in accordance with the information provided in the “VLAN Configuration” section above.
2. Drag a Cloud node in to the topology pane.
3. Change the icon to something other than a cloud. As mentioned above, I used a Firewall icon.
4. Change the cloud’s name to VSH-A.
5. Double click on the cloud. When the new window opens, click on the cloud’s name in the left pane.
6. From the “Generic Ethernet NIO” dropdown menu, select the VMnet1 interface and then click “Add”.

    The below image shows VMNet1 being selected for the VSH-A node:

    [![netw3](/assets/2015/02/netw31.png)](/assets/2015/02/netw31.png)

7. Using the table below, repeat Step 6 until all of the VSH-A VMnet interfaces have been added the cloud:
	
	<table>
	<tbody>
	<tr data-blogger-escaped-style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
	<td width="104" data-blogger-escaped-style="border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">Cloud Node:</strong></td>
	<td width="70" data-blogger-escaped-style="border-left: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;"><strong data-blogger-escaped-style="mso-bidi-font-weight: normal;">VMnet:</strong></td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 1;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-A</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet1</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 2;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-A</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet2</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 3;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-A</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet3</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 4;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-A</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet4</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 5;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-B</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet5</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 6;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-B</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet6</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 7;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-B</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet7</td>
	</tr>
	<tr data-blogger-escaped-style="mso-yfti-irow: 8; mso-yfti-lastrow: yes;">
	<td width="104" data-blogger-escaped-style="border-top: none; border: solid windowtext 1.0pt; mso-border-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 78.15pt;">VSH-B</td>
	<td width="70" data-blogger-escaped-style="border-bottom: solid windowtext 1.0pt; border-left: none; border-right: solid windowtext 1.0pt; border-top: none; mso-border-alt: solid windowtext .5pt; mso-border-left-alt: solid windowtext .5pt; mso-border-top-alt: solid windowtext .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 52.45pt;">VMnet8</td>
	</tr>
	</tbody>
	</table>

    **Important:** The order in which you add each VMnet interface to the Cloud node is very important. This is because when you’re linking these interfaces to other interfaces in GNS3, the VMnet interfaces will be listed in the same order but won’t be shown in an understandable format. For example, when connecting VSH-A to SW1 you will be presented with a list similar to this:

    [![netw4](/assets/2015/02/netw41.png)](/assets/2015/02/netw41.png)

    By adding the interfaces in the order which they appear in the table above, you will know that the first interface in the list is VMnet1, the second in the list is VMnet2, and so on. If you add them in a random order it will be difficult to decipher which is which.

8. Repeat Steps 2 through 7 for VSH-B.
9. Once all interfaces are mapped to their corresponding VSH icons, connect them to the destination ports as shown in the “VLAN Configuration” section above.
10. Connect VMnet4 and VMnet8 to R1 and R4’s Fa0/1 interfaces respectively.

### Router Configuration

To configure the routers, simply look at the “Final Diagram” in [**Part 1**](/guide-building-self-contained-virtual-steelhead-lab-part-1/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 1") and configure the interfaces accordingly, then use your favourite routing protocol(s) to enabling end to end connectivity.

### Transfer Speeds

Any traffic that flows through a GNS3 router is slowed dramatically. Generally it is not possible to get throughput of more than 5MB/sec. This is why, as per the “Final Diagram” in [**Part 1**](/guide-building-self-contained-virtual-steelhead-lab-part-1/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 1"), the two Windows XP VMs do not connect to the VSHs through a GNS3 router. If they did, you would not see any of the throughput improvements the VSHs provide.

Having said that though, it’s not all bad news. Because the two VSHs are separated by GNS3 routers the traffic that flows between the two "sites" is extremely slow. This results in the perfect testing environment to demonstrate the advantages of using features like SDR. (See the test results in [**Part 4**](/guide-building-self-contained-virtual-steelhead-lab-part-4/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 4") for more information).

If you’d like to connect your VSHs to real lab equipment and avoid the slow speeds GNS3 introduces, see the “Final Notes” section in [**Part 4**](/guide-building-self-contained-virtual-steelhead-lab-part-4/ "Guide: Building a Self-Contained Virtual Steelhead Lab – Part 4").