---
title: Subnetting Made Easy, Part 5
sub_heading: "A series of posts to help you become a subnetting expert!"
redirect_from: /subnetting-made-easy-part-5/

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
After publishing [**my previous post**](/subnetting-made-easy-part-4/), I received another e-mail from the author of the original e-mail:

_... So basically, I would like to find out how to find the first usable IP address. As I say, [**using your process**](/subnetting-made-easy-index/), I can figure out the Network IP address, and the Broadcast IP address, but I don't know how to figure out the first usable IP._

_I was asking someone in a class this morning, and he was saying something, that I didn't understand, about adding a "1" to the network address, which would make sense. If the Network IP address is 192.168.0.0 then the first usable address would be 192.168.0.1._

_The bit I don't get is, when you have the IP address and the Subnet Mask in binary, and the Subnet Mask is, say, 21 bits then that would leave 3 bits in the third octet belonging to the host part of the address. Therefore, in total there would be 11 bits belonging to the host address, but how is the calculation made to find the first host address? What happens to the three host bits in the third octet?_

My reply to this e-mail went as follows:

Your classmate was correct. As per my "[**Subnetting made Easy**](/subnetting-made-easy-formula/)" formula, the first usable address is the Network Address + 1. For example, if your subnet is 192.168.0.0/24 the first 24 bits (the first 3 octets) are dedicate to the network address. This leaves us with 8 bits are dedicated to hosts (the fourth octet). In other words, it looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="background-color: #fdff96; text-align: center;"><span style="font-size: 10pt;">H</span></td>
</tr>
</tbody>
</table>

In order to get a host address we need flip the least significant host bit (highlighted above) from a zero to a one. When we do this the binary in the 4th octet will look like this: 0000 0001 which translates to 1. This leaves us with: 192.168.0.1.

In regards to the /21, I used to struggle with this when I first studied networking too. However, once you get the hang of it you realise just how easy it really is. It might be easiest to picture it this way - When we talk about a /24 it looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
</tr>
</tbody>
</table>

*   If we leave all H bits as .0 we will find the **network address**.
*   If we add 1 to that, we will find the **first usable address**.
*   If we then flip all H's to 1, we fill find the **broadcast address**.
*   If we subtract 1 from the broadcast address, we will find the **last usable address**.

However, it's easy to get confused when the host bits aren't confined to the 4th octet. For example a /22 looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center;"><span style="text-decoration: underline;"><strong><span style="font-size: 10pt;">H</span></strong></span></td>
<td style="text-align: center;"><span style="text-decoration: underline;"><strong><span style="font-size: 10pt;">H</span></strong></span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">H</span></td>
</tr>
</tbody>
</table>

So now what we do? Well, we do exactly what we just did for the /24.

*   If we leave all H's as 0, we'll find the **network address**.
*   If we add 1 to the network address, we find the **first usable address**.
*   If we flip all H's (including the ones in the 3rd octet) to 1, we find the **broadcast address**.
*   If we subtract 1 from the broadcast address, we will find the **last usable address**.

So you see, it doesn't matter how many octets the H bits are spread across, this process is the same every time.

Just in case the penny hasn't dropped yet, let's focus on the HH in the third octet of the example above. These H's represent two binary bits and therefore they give us four combinations:

<table>
<tbody>
<tr>
<td style="text-align: center;">H</td>
<td style="text-align: center;">H</td>
<td style="text-align: center;">Third Octet Binary:</td>
<td style="text-align: center;">Total:</td>
<td style="text-align: center;">Network:</td>
<td style="text-align: center;">Name:</td>
</tr>
<tr>
<td style="text-align: center;"><span style="color: #ff0000;">0</span></td>
<td style="text-align: center;"><span style="color: #0000ff;">0</span></td>
<td style="text-align: center;">0000 00<strong><span style="color: #ff0000;">0</span><span style="color: #0000ff;">0</span></strong></td>
<td style="text-align: center;">0</td>
<td style="text-align: center;">192.168.<strong>0</strong>.0</td>
<td style="text-align: center;">1st</td>
</tr>
<tr>
<td style="text-align: center;"><span style="color: #ff0000;">0</span></td>
<td style="text-align: center;"><span style="color: #0000ff;">1</span></td>
<td style="text-align: center;">0000 00<strong><span style="color: #ff0000;">0</span><span style="color: #0000ff;">1</span></strong></td>
<td style="text-align: center;">1</td>
<td style="text-align: center;">192.168.<strong>1</strong>.0</td>
<td style="text-align: center;">2nd</td>
</tr>
<tr>
<td style="text-align: center;"><span style="color: #ff0000;">1</span></td>
<td style="text-align: center;"><span style="color: #0000ff;">0</span></td>
<td style="text-align: center;">0000 00<strong><span style="color: #ff0000;">1</span><span style="color: #0000ff;">0</span></strong></td>
<td style="text-align: center;">2</td>
<td style="text-align: center;">192.168.<strong>2</strong>.0</td>
<td style="text-align: center;">3rd</td>
</tr>
<tr>
<td style="text-align: center;"><span style="color: #ff0000;">1</span></td>
<td style="text-align: center;"><span style="color: #0000ff;">1</span></td>
<td style="text-align: center;">0000 00<strong><span style="color: #ff0000;">1</span><span style="color: #0000ff;">1</span></strong></td>
<td style="text-align: center;">3</td>
<td style="text-align: center;">192.168.<strong>3</strong>.0</td>
<td style="text-align: center;">4th</td>
</tr>
</tbody>
</table>

1.  If we leave the two H bits in the 3rd octet as 0 (as per 1st), we end up with: 192.168.0.0.
2.  If we flip the least significant bit in the 3rd octet to a one (as per "2nd" above), we end up with: 192.168.1.0.
3.  If use the  "3rd" option above, we end up with: 192.168.2.0.
4.  If use the  "4th" option above, we end up with: 192.168.3.0.

Note that as these are H bits, they are all part of the same subnet. If they were N bits, they would represent separate subnets. In other words, 192.168.0.0 - 192.168.0.255, 192.168.1.0 - 192.168.1.255, 192.168.2.0 - 192.168.2.255, 192.168.3.0 - 192.168.3.255 are all on the same subnet (Layer 2 domain). This ranges can be written as 192.168.0.0 - 192.168.3.255, or simply 192.168.0.0 /22.

One last thing just to hammer the point home :) If a host in any of these IP ranges sent a broadcast, all other hosts in these IP ranges would receive it. This is because, as mentioned above, we are dealing with a /22 subnet. If on the other hand we were talking about about /24 ranges, 192.168.0.0 - 192.168.0.255, 192.168.1.0 - 192.168.1.255, 192.168.2.0 - 192.168.2.255, 192.168.3.0 - 192.168.3.255 would all be part of separate subnets (Layer 2 domains).

## Other Posts in this Series

See the **[Subnetting Made Easy - Index](/subnetting-made-easy-index/)** post for links to all of the posts in this series.