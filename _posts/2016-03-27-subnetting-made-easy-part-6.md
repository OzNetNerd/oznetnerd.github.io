---
title: Subnetting Made Easy, Part 6
sub_heading: "A series of posts to help you become a subnetting expert!"
redirect_from: /subnetting-made-easy-part-6/

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
**Note:** As you will soon see I use tables (some might say excessively :P) in this post. I did this is because it closely resembles what a lot of network engineers do when they're performing subnet calculations with a pen and paper. This is a very important skill to learn as you won't always have access to a subnet calculator when you need one.

I recently received an e-mail from a reader asking for subnetting assistance. An extract of the e-mail is below.

_I looked at your **[subnetting archive](/subnetting-made-easy-index/)** but I am still confused on how to subnet! *sobs*_

_So could you help me subnet a IP address of 192.168.25.0/24 with 2 and 27 hosts._

_It's been a while since I've done this and your blog is the closest guide that's helped somewhat._

_Could you explain to me step by step how to find the:_

1.  _Number of bits in the subnet_
2.  _The subnet mask in binary_
3.  _The subnet mask in decimal_
4.  _The maximum number of usable subnets including the 0 subnet_
5.  _The number of usable hosts per subnet_
6.  _The first and last host address  for each subnet_

## Refresh our Memory

Before I answer the above questions let's refresh our memory on a couple of things.

### **Binary Refresher**

Below are 11 binary bits:

<table>
<tbody>
<tr>
<td style="text-align: center;"><span style="font-size: 10pt;">1024</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">512</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">256</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">128</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">64</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">32</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">16</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">8</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">4</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">2</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">1</span></td>
</tr>
</tbody>
</table>

_Table 1_

Binary is read from right to left and it increases exponentially. In order to convert binary to decimal we need to add up all binary values that have a 1 aligned to them and ignore all the values that have a 0 aligned to them. Here are a couple of examples:

## Example 1:

<table>
<tbody>
<tr>
<td style="text-align: center;"><em><strong><span style="font-size: 10pt;">1024</span></strong></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">512</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">256</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">128</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">64</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">32</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">16</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">8</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">4</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">2</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">1</span></em></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">Total:</span></strong></td>
</tr>
<tr>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1</span></strong></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1024</span></strong></td>
</tr>
</tbody>
</table>

_Table 2_

### Example 2:

<table>
<tbody>
<tr>
<td style="text-align: center;"><em><span style="font-size: 10pt;">1024</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">512</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">256</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">128</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">64</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">32</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">16</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">8</span></em></td>
<td style="text-align: center;"><em><strong><span style="font-size: 10pt;">4</span></strong></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">2</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">1</span></em></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">Total:</span></strong></td>
</tr>
<tr>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1</span></strong></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">4</span></strong></td>
</tr>
</tbody>
</table>

_Table 3_

### Example 3:

<table>
<tbody>
<tr>
<td style="text-align: center;"><em><strong><span style="font-size: 10pt;">1024</span></strong></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">512</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">256</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">128</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">64</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">32</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">16</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">8</span></em></td>
<td style="text-align: center;"><em><strong><span style="font-size: 10pt;">4</span></strong></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">2</span></em></td>
<td style="text-align: center;"><em><span style="font-size: 10pt;">1</span></em></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">Total:</span></strong></td>
</tr>
<tr>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1</span></strong></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1</span></strong></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center;"><strong><span style="font-size: 10pt;">1028</span></strong></td>
</tr>
</tbody>
</table>

_Table 4_

#### **IP Addressing Refresher**

Here are some quick points on IP addressing. Note that the below dot points apply to IP addresses as well as network and subnet addresses:

*   There are four octets in an IP address.
*   Each octet is made up of eight bits (which is why they're called octets).
*   There are 32 bits in an IP address (4 octets x 8 bits per octet = 32).
*   Each octet is separated by a "dot", e.g 10.45.32.2 - the first octet is 10, the second is 45, the third is 32 and the fourth is 2.
*   Each octet is composed of the following bit ranges:

<table>
<tbody>
<tr>
<td style="text-align: center;"><strong>Octet:</strong></td>
<td style="text-align: center;"><strong>Bits:</strong></td>
</tr>
<tr>
<td style="text-align: center;">1</td>
<td style="text-align: center;">1 to 8</td>
</tr>
<tr>
<td style="text-align: center;">2</td>
<td style="text-align: center;">9 to 16</td>
</tr>
<tr>
<td style="text-align: center;">3</td>
<td style="text-align: center;">17 to 24</td>
</tr>
<tr>
<td style="text-align: center;">4</td>
<td style="text-align: center;">25 to 32</td>
</tr>
</tbody>
</table>

_Table 5_

#### **Subnetting Refresher**

As you might recall the "slash" number at the end of a network address tells us how many bits are "network" (N) bits. For example a /27 means that the first 27 bits of the IP address are used to define the network the address resides. The remaining 5 bits (32 bits in an IP address - 27 network bits = 5) are known as "host" (H) bits.

These H bits can be left as they are in order to maximise the number of host addresses you can have or they can be converted to "subnet" bits (SN) in order to create subnets. Note that H and SN bits are inversely proportional in that the more H bits you convert to SN bits the more subnets you'll get but the number of host addresses per subnet will be reduced.

_Note: When you convert H bits to SN bits you calculate the new "slash" number by adding the SN and N bits together. For example if you start with a /24 network, you've got 8 H bits. If you decide you want to convert 5 H bits into SN bits, these 5 bits will be added to the 24 N bits resulting in the subnets being /29s and you being left with 3 H bits._

It is important to keep in mind that regardless of how you choose to use the H bits one fact remains the same - you cannot **reduce **the number of N bits. If you've been given a /24 network you cannot change it to something smaller, e.g /23. Doing so can lead to IP overlaps which will result in an unstable network.

#### **Network & Host Boundary Refresher**

This section isn't related to the e-mail I but I thought it is worth a mention anyway. When you're dealing with a /8, /16 or /24 network, your N bits and your H bits will be in two different octets. The reason for this is that these N bits are at the edge of the 1st, 2nd and 3rd octets respectively. This therefore means that the H bits will begin in the 2nd, 3rd and 4th octets respectively.

The above might be a little difficult to understand so here are a couple of examples which should clear things up:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>4th Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"><strong>Network</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">10</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">15</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">/8</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">172</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">16</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">1</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">43</td>
<td style="text-align: center; vertical-align: middle;">/16</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">32</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">63</td>
<td style="text-align: center; vertical-align: middle;">/24</td>
</tr>
</tbody>
</table>

_Table 6_

We can compare the "Network" column from Table 6 to the information in Table 5 to identify which octet the N and H bits relate to. Doing this confirms what I mentioned above - the /8, /16 and /24's network bits are the last bits in the 1st, 2nd and 3rd octets respectively. This then means that the H bits for these networks will begin at 9th, 17th and 25th bit respectively which means they reside in the 2nd, 3rd and 4th octets respectively.

To put it another way, the 10.30.15.25/8 would look like this:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">Bits 1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 25 to 32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">10</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">15</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">25</td>
</tr>
</tbody>
</table>

_Table 7_

So you see, the /8 (also known as 8 N bits) reside in the 1st Octet and the H bits start from the 2nd Octet (the 9th bit onwards).

If on the other hand we were given a /7 network, both the N and the H bits would be in the 1st octet as the H bits would start from the 8th bit.

I felt it was important to point this out because you're not always going to have N and H bits reside in two different octets which sometimes causes confusion for newcomers.

### **2 ^ n and 2 ^ n - 2 Formula Refreshers**

Before I describe when these two formulas are used let's first discuss what _2 ^ n_ means. _2 ^ n_ simply means two to the power of a number (_n_ is a placeholder for the number). An example would be 2 ^ 1 = 2. Another example would be  2 ^ 5 = 32.

If you're thinking to yourself "counting in binary is hard enough, how am I going to remember how to do this as well?!?!", let me assure you it's very easy. All you do is start counting from 2 then double it, then continue doubling the resulting number each time. If you're still confused have a look at this table:

<table>
<tbody>
<tr>
<td style="text-align: center;" colspan="2"><strong>2 ^ n</strong></td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 1</td>
<td style="text-align: center;">2</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 2</td>
<td style="text-align: center;">4</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 3</td>
<td style="text-align: center;">8</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 4</td>
<td style="text-align: center;">16</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 5</td>
<td style="text-align: center;">32</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 6</td>
<td style="text-align: center;">64</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 7</td>
<td style="text-align: center;">128</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 8</td>
<td style="text-align: center;">256</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 9</td>
<td style="text-align: center;">512</td>
</tr>
<tr>
<td style="text-align: center;">2 ^ 10</td>
<td style="text-align: center;">1024</td>
</tr>
</tbody>
</table>

_Table 8_

That's not so hard is it? :) If you're still confused though try using this formula instead: _n + n_. As mentioned above we start counting from 2, therefore the first calculation we would need to do is _2 + 2 = 4_. Next it would be _4 + 4 = 8_, then _8 + 8 = 16_, and so on.

So now that we know how to calculate _2 ^ n_ all we need to do to obtain the result of the _2 ^ n - 2_ formula is subtract two from each of the results in Table 8. For example, _2 ^ 7 - 2 = 126_.

Now let's discuss why we need these formulas. Sometimes you will come across situations where you're given a single network address (e.g 192.168.43.0/24) and you're asked to divide it into X number of subnets and/or ensure there are Y number of host addresses available in each subnet. These formulas are used to provide answers to these questions. How you ask? As I mentioned earlier in this post, _"H bits can be left as they are in order to maximise the number of host addresses you can have or they can be converted to "subnet" bits (SN) in order to create subnets"._ The _2 ^ n_ formula is used to calculate how many subnets we can get by using _n_ number of H bits and the _2 ^ n - 2 _formula is used to calculate how many usable host addresses we can get per subnet by using _n_ number of H bits.

To demonstrate this let's run through a few example questions:

### Example Question 1:

_Your client gives you the 192.168.41.0/24 network and asks you to provide them with 8 subnets. How many H bits are required to satisfy their request?_

Because we've been given a /24, this means there are 24 "reserved" N bits (the 1st bit to the 24th bit) and 8 H bits (the 25th bit to the 32nd bit). Remember that we are not allowed to reduce the number of N bits (which is why I say they're "reserved"), therefore we'll need to use some of the H bits in order to create these subnets. Before we do, let's put this information into a table as it will be easier to visualise:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">26</td>
<td style="text-align: center; vertical-align: middle;">27</td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">41</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 9_

_Note that I have broken down the 4th octet into each of its individual bits._

In order to answer this question all we need to do is use the _2 ^ n_ formula. As we saw in Table 8, 2 ^ 3 = 8 which tells us the answer to the question is 3. When we put this information into the above table it looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">26</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">27</span></td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">41</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 10_

This results in the new network address being 192.168.41.0**/27**. This is because as mentioned above, the SN bits are added to N bits (24 N bits + 3 SN bits = 27).

**Bonus Points**: How many usable addresses will each subnet have?

**Answer:** As we've got 5 H bits left the _2 ^ n - 2_ formula looks like this:

2 ^ 5 - 2 = 30 usable addresses

### Example Question 2:

_Your client gives you the 172.16.54.0/26 network and asks you to break it down into subnets which contain 14 usable addresses per subnet. How many H bits are required to satisfy their request?_

Because we've been given a /26 this time it means there are 26 "reserved" N bits (the 1st bit to the 26th bit) and 6 H bits (the 27th bit to the 32nd bit). Let's put this information into a table again:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">26</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">27</span></td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">H</span></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">41</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 11_

In order to answer this question all we need to do is use the _2 ^ n - 2_ formula. As per Table 8 we see that 2 ^ 4 = 16, then if we subtract 2 we're left with 14. Therefore the answer to this question is 4. This leaves us with 2 extra H bits which, although not a required to answer the question, can be converted to SN bits. If we did decide to convert them to SN bits it would look like this:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">26</span></td>
<td style="text-align: center; vertical-align: middle;">27</td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #00ffff;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #00ffff;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">H</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">41</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 12_

**Bonus Points**: How many subnets can we have?

**Answer:** 2 ^ 2 - 4 = 4 subnets

### Example Question 3:

Warning, this is a trick question. See if you can answer it before you read the solution.

_Your client gives you the 10.82.162.0/24 _network and asks you to break it down into subnets which contain 63 usable addresses per subnet. How many H bits are required to satisfy their request?__

**Solution:** I say this question is a trick question because if you look at Table 8 the first answer that pops into your head might be 6. That is because 2 ^ 6 = 64. If that happened to you then unfortunately that is incorrect. The reason being that that formula (_2 ^ n_) is used to calculate subnets, not usable addresses. When 6 is put into the correct formula (_2 ^ n - 2_), it looks like this: 2 ^ 6 - 2 = 62 which is one less than what the client requested. This therefore means the correct answer is 7. You might now be thinking 2 ^ 7 - 2 = 126 - That's a whole lot more usable address than what the client asked for! This is true but it is the only way they will be able to fit 63 hosts onto a single subnet.

**Note:** These types of questions are likely to come up in real life scenarios as well as in exams and **[job interviews](/land-dream-job/)**, so make sure you thoroughly understand how these formulas work.

### Questions Answered

Now that we've refreshed our memory on the topics above let's dive into answering the questions contained in the e-mail. Keep in mind that the author of the e-mail specified a network address 192.168.25.0/24 and is looking for answers that apply to subnets which contain 2 hosts and 27 hosts. This means there will be two answers for every question. I will provide the answers under the titles "**2 Hosts per Subnet Answer**" and "**27 Hosts per Subnet Answer**".

### **Question 1**

H_ow many H bits are left over when 192.168.25.0/24 is subnetted in such a way that it creates 2 hosts and 27 hosts per subnet?_

As mentioned in the "Subnetting Refresher" section, the "slash" number tells us how many N bits there are in the network address. As the address in question is a /24, we know that the first 24 bits (1 to 24) are N bits and therefore reserved. This leaves us with 8 H bits (25 to 32) to do with what we please.

In other words the 32 bits in the network address are broken down in the following fashion at this point in time:

*   24 Network (N) bits
*   0 Subnet (SN) bits
*   8 Host (H) bits

Let's also put that information into a table:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">Bits 1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">Bits 25 to 32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">0</td>
</tr>
</tbody>
</table>

_Table 13_

Now in order to find out how many H bits we need let's use our trusty _2 ^ n - 2_ formula, like so:

*   2 Hosts per Subnet: 2 H bits ^ 4 - 2 = 2 hosts.
*   27 Hosts per Subnet: 5 H bits ^ 5 - 2 = 30 hosts (note that this is higher than the requested 27 but it is the only valid option and therefore could be a trick question like the one I wrote earlier in this post).

The above tells us that:

### 2 Hosts per Subnet Answer

As per the calculation above we need to use 2 H bits in order to achieve our goal of two hosts per subnet. As we originally had 8 H bits but we 're only using 2 of them we're left with 6 H bits which answers the author's question.

Though didn't ask for it, let's look at what happens when we turn the remaining 6 H bits into SN bits:

*   24 Network (N) bits
*   6 Subnet (SN) bits
*   2 Host (H) bits

When put into a table it looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">26</td>
<td style="text-align: center; vertical-align: middle;">27</td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">30</span></td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 14_

We can use the _2 ^ n_ formula to calculate that these 6 SN bits will provide us with 64 subnets. Each of these subnets contain 2 usable IP addresses. (See the answers to Questions 4 and 5 below for more information).

By adding the N and SN bits together we get the total number of N bits for these new subnets, /30.

### 27 Hosts per Subnet Answer

As per the calculation above we need to use 5 H bits in order to achieve our goal of 27 hosts per subnet. As we originally had 8 H bits but we 're only using 5 of them we're left with 3 H bits which answers the author's question.

Let's look at what happens when we turn the remaining 3 H bits into SN bits:

*   24 Network (N) bits
*   3 Subnet (SN) bits
*   5 Host (H) bits

When put into a table it looks like this:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>1st Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>2nd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><strong>3rd Octet</strong></td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong>4th Octet</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Bits:</strong></td>
<td style="text-align: center; vertical-align: middle;">1 to 8</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">9 to 16</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">17 to 24</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">26</td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffcc00;">27</span></td>
<td style="text-align: center; vertical-align: middle;">28</td>
<td style="text-align: center; vertical-align: middle;">29</td>
<td style="text-align: center; vertical-align: middle;">30</td>
<td style="text-align: center; vertical-align: middle;">31</td>
<td style="text-align: center; vertical-align: middle;">32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">N</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="background-color: #ffff00;">SN</span></td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
<td style="text-align: center; vertical-align: middle;">H</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">168</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;">25</td>
<td style="text-align: center; vertical-align: middle;">.</td>
<td style="text-align: center; vertical-align: middle;" colspan="8">0</td>
</tr>
</tbody>
</table>

_Table 15_

We can use the _2 ^ n_ formula to calculate that these 3 SN bits will provide us with 8 subnets. Each of these subnets contain 30 usable IP addresses. (See the answers to Questions 4 and 5 below for more information).

By adding the N and SN bits together we get the total number of N bits for these new subnets, /27.

### **Question 2 & 3**

_What are the subnet masks in binary and decimal?_

This can be a tricky one to understand for newcomers, but it is actually very easy once you get the hang of it.

Let's recap on the information we've obtained so far. As we saw in previous section to have 2 hosts per subnet we use 30 N bits, and to have 27 hosts (well 30 to be exact) per subnet we use 27 N bits. This can also be written as /30 and /27 respectively. Further to this, the network address we're basing our calculations off is 192.168.25.0. Now that we've got that out of the way let's get started!

First we need to see which octet the H bits reside. As we're dealing with a /30 and a /27 mask Table 5 tells us that the H bits reside in the 4th octet for both networks, therefore let's break down the 4th octet only:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;" colspan="5"><strong><span style="font-size: 10pt;">1st, 2nd &amp; 3rd Octets&nbsp;</span></strong></td>
<td style="text-align: center; vertical-align: middle;"><strong><span style="font-size: 10pt;">&nbsp;</span></strong></td>
<td style="text-align: center; vertical-align: middle;" colspan="8"><strong><span style="font-size: 10pt;">4th Octet</span></strong></td>
<td style="text-align: center; vertical-align: middle;"><strong><span style="font-size: 10pt;">Network Bits</span></strong></td>
<td style="text-align: center; vertical-align: middle;"><strong><span style="font-size: 10pt;">Subnet Mask</span></strong></td>
<td style="text-align: center; vertical-align: middle;"><strong><span style="font-size: 10pt;">4th Octet Calculation</span></strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong><span style="font-size: 10pt;">Bits:</span></strong></td>
<td style="text-align: center; vertical-align: middle;" colspan="5"><span style="font-size: 10pt;">1 to 24&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">25</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">26</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffff00;"><strong>27</strong></span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">28</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">29</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffff00;"><strong>30</strong></span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">31</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">32</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">&nbsp;</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;" rowspan="7"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;" colspan="5"><span style="font-size: 10pt;">&nbsp;Decimal</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">&nbsp;</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">128</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">64</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">32</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">16</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">8</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">4</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">2</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">1</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;" colspan="5" rowspan="2"><span style="font-size: 10pt;">192.168.25</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><strong><span style="font-size: 10pt;">/24</span></strong></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">255.255.255.0</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">N/A</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; line-height: 20px;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 13.3333px; line-height: 20px;">0</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;" colspan="5" rowspan="2"><span style="font-size: 10pt;">192.168.25</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">&nbsp;N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;"><strong><span style="background-color: #ffff00;">N</span></strong></span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><strong><span style="font-size: 10pt; background-color: #ffff00;">/27</span></strong></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">255.255.255.224</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;"><span style="background-color: #ffcc00;">128 + 64 + 32</span> = 224</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;" colspan="5" rowspan="2"><span style="font-size: 10pt;">192.168.25</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">.</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">&nbsp;N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">N</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;"><strong><span style="background-color: #ffff00;">N</span></strong></span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">H</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><strong><span style="font-size: 10pt; background-color: #ffff00;">/30</span></strong></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;">255.255.255.252</span></td>
<td style="text-align: center; vertical-align: middle;" rowspan="2"><span style="font-size: 10pt;"><span style="background-color: #ffcc00;">128 + 64 + 32 + 16 + 8 + 4</span> = 252</span></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt; background-color: #ffcc00;">1</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
<td style="text-align: center; vertical-align: middle;"><span style="font-size: 10pt;">0</span></td>
</tr>
</tbody>
</table>

_Table 16_

As H bits do not contribute to the subnet mask calculation (the subnet mask will remain the same regardless of whether the H bits are 1's, 0's or a combination of the two), I have set them to 0. N bits on the other hand are always set to 1 when we're calculating subnet masks. This means that as the first three octets are "reserved" N bits we know they are going to be 255 (128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 = 255). This is why there was no need to break down these octets. The fourth octet on the other hand differs which is why we did need to break it down.

Note that as per the "Calculation" column in Table 16, in order to find out the value of the fourth octet all we need to do is add up all of the N binary bits.

### 2 Hosts per Subnet Answer

Subnet Mask in Binary: 1111 1100

Subnet Mask in Decimal: 255.255.255.252

### 27 Hosts per Subnet Answer

Binary Answer: 1110 0000

Decimal Answer: 255.255.255.224

### **Question 4**

_What is the maximum number of usable subnets including the 0 subnet for each of the masks?_

If you're wondering why the author asked me to include subnet 0, it is because quite some time ago it was recommended that you do not use the first and last networks when subnetting. As this recommendation  has been invalid for a while now you most probably won't hear anyone speaking about it these days. If you're interested in reading more about it though please have a read of **[this page](http://www.cisco.com/c/en/us/support/docs/ip/dynamic-address-allocation-resolution/13711-40.html)**.

In regards to answering this question, the first thing we need to do is find out how many SN bits we have. As you might recall though we already figured this out when answering Question 1. We found that there are 6 SN bits when we've got 2 hosts per subnet and 3 SN bits when we've got 27 hosts (30 to be exact) per subnet.

Armed with this information answering the question is as easy as using the _2 ^ n_ formula discussed earlier in this post.

#### 2 Hosts per Subnet Answer

2 ^ 6 = 64 subnets

#### 27 Hosts per Subnet Answer

2 ^ 3 = 8 subnets

### **Question 5**

_What is the number of usable host addresses per subnet for each of the masks?_

To answer this question we follow a process that is very similar to the one we used to answer Question 4.  The first thing we need to do is find out how many H bits we have. Like Question 4, we found this out when answering Question 1. We found that we need 2 H bits when we want 2 hosts per subnet and 5 H bits when want 27 hosts per subnet.

Armed with this information answering the question is as easy as using the _2 ^ n - 2_ formula discussed earlier in this post.

#### 2 Hosts per Subnet Answer

2 ^ 2 - 2 = 2 hosts

#### 27 Hosts per Subnet Answer

2 ^ 5 = 30 hosts

### **Question 6**

_What is the first and last host address of the 192.168.25.0/27 and 192.168.25.0/30__ subnets?_

I've written **[a couple of posts](/subnetting-made-easy-formula/)** about a formula I use to answer this very question. For ease of reference I have posted the formula below.

*   **First Address** = Network Address + 1
*   **Broadcast Address** = Next Network Address – 1
*   **Last Address** = Broadcast Address – 1

Let's apply this formula to the networks mentioned in the e-mail:

#### 2 Hosts and 27 Hosts per Subnet Answers

<table>
<tbody>
<tr>
<td style="text-align: center;"><strong>Network Address</strong></td>
<td style="text-align: center;"><strong>First Address</strong></td>
<td style="text-align: center;"><strong>Last Address</strong></td>
<td style="text-align: center;"><strong>Broadcast Address</strong></td>
</tr>
<tr>
<td style="text-align: center;">192.168.25.0 /24</td>
<td style="text-align: center;">192.168.25.1</td>
<td style="text-align: center;">192.168.25.254</td>
<td style="text-align: center;">192.168.25.255</td>
</tr>
<tr>
<td style="text-align: center;">192.168.25.0 /27</td>
<td style="text-align: center;">192.168.25.1</td>
<td style="text-align: center;">192.168.25.30</td>
<td style="text-align: center;">192.168.25.31</td>
</tr>
<tr>
<td style="text-align: center;">192.168.25.0 /30</td>
<td style="text-align: center;">192.168.25.1</td>
<td style="text-align: center;">192.168.25.2</td>
<td style="text-align: center;">192.168.25.3</td>
</tr>
</tbody>
</table>

_Table 17_

I'll now break down the steps I used to fill in in each cell of Table 17.

##### First Usable Address

Finding the first usable address is easy because we were given the Network addresses to begin with, all we have to do is add 1 to each of them and we've got our first usable addresses. As the network address is the same across the board, the first usable address is also the same.

#### 2 Hosts and 27 Hosts per Subnet Answer

192.168.25.0 + 1 = 192.168.25.1

Therefore the First Usable Address for the 192.168.25.0/30 and 192.168.25.0/27 networks is 192.168.25.1.

#### Broadcast Address

To work out the broadcast address we need to work out the **next** network address. To do this we'll to take another look at Table 16. We can see that the last N bit for the /27 network is aligned to the binary bit which represents decimal 32 while the last N bit for the /30 network is aligned to the binary bit which represents decimal 4. Now all we do is increment the 192.168.25.0/27 and 192.168.25.0/30 networks by 32 and 4 respectively and we'll have the next network address which is 192.168.25.32/27 and 192.168.25.4/30 respectively. Though it's not a requirement we can continue to increment these networks to find out the network addresses of the subsequent networks too, like so:

<table>
<tbody>
<tr>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;" colspan="2"><strong>Network Address</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;"><strong>Subnet:</strong></td>
<td style="text-align: center; vertical-align: middle;"><strong>/27</strong></td>
<td style="text-align: center; vertical-align: middle;"><strong>/30</strong></td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">1</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.0</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.0</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">2</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.32</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.4</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">3</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.64</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.8</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">4</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.96</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.12</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">5</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.128</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.16</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">6</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.160</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.20</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">7</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.192</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.24</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">8</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.224</td>
<td style="text-align: center; vertical-align: middle;">192.168.25.28</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">9</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192.168.25.32</td>
</tr>
<tr>
<td style="text-align: center; vertical-align: middle;">10</td>
<td style="text-align: center; vertical-align: middle;"></td>
<td style="text-align: center; vertical-align: middle;">192.168.25.36</td>
</tr>
</tbody>
</table>

_Table 18_

_Side Note: As you can see in Table 18 we could only get eight /27 subnets. This marries up with the 2 ^ n formula discussed earlier in this post as 2 ^ 3  = 8 (see Table 8). I decided to stop at 10 for the /30 subnets because 2 ^ 6 = 64 and I didn't want to bore you by creating a table with 64 rows :)_

Now that we know the **next** network addresses we're able to find the broadcast addresses by simply subtracting 1 from them:

#### 2 Hosts per Subnet Answer

192.168.25.4 - 1 = 192.168.25.3

Therefore the Broadcast Address for the 192.168.25.0/30 network is 192.168.25.3.

#### 27 Hosts per Subnet Answer

192.168.25.32 - 1 = 192.168.25.31

Therefore the Broadcast Address for the 192.168.25.0/27 network is 192.168.25.31.

##### Last Usable Address

To find the last usable address we subtract 1 from the broadcast address. It really can't get easier than that, can it?

#### 2 Hosts per Subnet Answer

192.168.25.3 - 1 = 192.168.25.2

Therefore the last usable address for the 192.168.25.0/30 is 192.168.25.2.

#### 27 Hosts per Subnet Answer

Therefore the last usable address for the 192.168.25.0/27 is 192.168.25.30.

##  Conclusion

Congratulations, you've made it to the end of this post! I know it was quite a long one but I wanted to make sure I was thorough as possible :) I hope you found this post informative. Happy Subnetting!

## Other Posts in this Series

See the **[Subnetting Made Easy - Index](/subnetting-made-easy-index/)** post for links to all of the posts in this series.