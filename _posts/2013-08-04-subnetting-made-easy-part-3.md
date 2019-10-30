---
title: Subnetting Made Easy, Part 3
sub_heading: "A series of posts to help you become a subnetting expert!"
redirect_from: /subnetting-made-easy-part-3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Cisco
- Subnetting

tags:
- Cisco
- Subnetting
---
**Note:** For the first post in this series, please see the [**Subnetting Made Easy, Part 1**](/subnetting-made-easy-part-1/ "Subnetting Made Easy, Part 1") post.

Recently I received an e-mail from a reader who was having trouble with one of their labs. The lab required them to write an ACL which matches only **part** of a subnet, as opposed to the whole subnet which is what is commonly seen in lab environments. The details are as follows:

*   **Network Address: **192.168.0.192 /27
*   **Usable Addresses:** 192.168.0.193 - 192.168.0.222
*   **Subnet Mask:** 255.255.255.224
*   **Wildcard Mask:** 0.0.0.31

The range of addresses inside the subnet which need to be matched are:

**Match Addresses:** 192.168.0.193 - 192.168.0.206

In order to create the ACL, the author needs to find out which wildcard mask matches only the above mentioned addresses.

## The Solution

The solution I use to answer this question is the same one I use when I'm summarising routes. I convert the necessary octet to binary and then find the least significant bit that matches both the lowest IP address and highest IP address in the range. In this case the details are as follows:

*   **Octet:** 4th (given that the network address is a /27)
*   **Lowest IP:** 192.168.0.193
*   **Highest IP:** 192.168.0.206

<table>
<tbody>
<tr>
<td style="text-align: center;" rowspan="2"></td>
<td style="text-align: center;">/25</td>
<td style="text-align: center;">/26</td>
<td style="text-align: center;">/27</td>
<td style="text-align: center;"><span style="color: #ff0000;"><strong>/28</strong></span></td>
<td style="text-align: center;">/29</td>
<td style="text-align: center;">/30</td>
<td style="text-align: center;">/31</td>
<td style="text-align: center;">/32</td>
<td style="text-align: center;" rowspan="2"></td>
</tr>
<tr>
<td style="text-align: center;">128</td>
<td style="text-align: center;">64</td>
<td style="text-align: center;">32</td>
<td style="text-align: center;"><span style="color: #ff0000;"><strong>16</strong></span></td>
<td style="text-align: center;">8</td>
<td style="text-align: center;">4</td>
<td style="text-align: center;">2</td>
<td style="text-align: center;">1</td>
</tr>
<tr>
<td style="text-align: center;"><strong>Network:</strong></td>
<td style="text-align: center;"><span style="color: #0000ff;"><strong>1</strong></span></td>
<td style="text-align: center;"><span style="color: #0000ff;"><strong>1</strong></span></td>
<td style="text-align: center;">0</td>
<td style="text-align: center;"><span style="color: #ff0000;"><strong>0</strong></span></td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">= 192</td>
</tr>
<tr>
<td style="text-align: center;"><strong>Lowest IP:</strong></td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;"><span style="color: #ff0000;"><strong>0</strong></span></td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">= 193</td>
</tr>
<tr>
<td style="text-align: center;"><strong>Highest IP:</strong></td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;"><span style="color: #ff0000;"><strong>0</strong></span></td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">= 206</td>
</tr>
</tbody>
</table>

As highlighted above, first, second third and fourth most significant bits match. The fifth bit however, does not. This therefore means the fourth bit is the one we're looking for. From this we can deduce the following:

*   **Network Address:** 192.168.0.192 (the 192 is because of the **128 + 64** as shown above)
*   **Subnet Mask:** 255.255.255.240 (**/28**) - (This is because the four most significant bits match)
*   **Wildcard Mask:** 0.0.0.15 - (Inverse of the Subnet Mask - 255 - 240 = 15)

## Other Posts in this Series:

See the **[Subnetting Made Easy - Index](/subnetting-made-easy-index/)** post for links to all of the posts in this series.