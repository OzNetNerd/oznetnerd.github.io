---
title: Subnetting Made Easy, Part 1
sub_heading: "A series of posts to help you become a subnetting expert!"
redirect_from: /subnetting-made-easy-part-1/

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
There are a wide range of techniques people use to work out their network, host and broadcast addresses. I prefer to take the binary approach as I find it the quickest and easiest method, and is never wrong.

Remember, the four most important things to know about a subnet is the following:

* **Network Address:**
* **First Usable Address:**
* **Last Usable Address:**
* **Broadcast Address:**

Let's say for example, we were given the IP address **195.70.16.159** and told that it is in a /30. This is how I'd go about filling in the template above...

First of all, as IP addresses are 32 bits long, and each octet is 8 bits in length, we know that:

*   **Bits 0 to 8** are covered in the first octet.
*   **Bits 9 to 16** are covered in the second octet.
*   **Bits 17 to 24** are covered in the third octet.
*   **Bits 25 to 32** are covered in the fourth octet.

So, as this subnet address has 30 bits in it, we know we're dealing with the fourth octet.

Now, because know bits 25 to 30 are subnet bits (referred to as SN below), we also know that the remaining two bits are host bits (referred to H below). Here is what it looks like when written down:

|||||||||
|--- |--- |--- |--- |--- |--- |--- |--- |
|25|26|27|28|29|30|31|32|
|SN|SN|SN|SN|SN|SN|H|H|
|x|x|x|x|x|x|x|x|

Now let's replace the bit numbers with their values:

|||||||||
|--- |--- |--- |--- |--- |--- |--- |--- |
|128|64|32|16|8|4|2|1|
|SN|SN|SN|SN|SN|SN|H|H|
|x|x|x|x|x|x|x|x|

Now, let's replace the x's with the value of the fourth octet in the address, which in this case, is 159.

|||||||||
|--- |--- |--- |--- |--- |--- |--- |--- |
|128|64|32|16|8|4|2|1|
|SN|SN|SN|SN|SN|SN|H|H|
|1|0|0|1|1|1|1|1|

If you are wondering how I came up with the above, it is very simple. All I did was:

*   Subtract 128 from 159, which left me with 31
*   I then subtracted 16 from 31, whcih left me with 15
*   I then subtracted 8 from 15, which left me with 7
*   I then subtracted 4 from 7, which left me with 3
*   I then subtracted 2 from 3, which left me with 1
*   I then subtracted 1 from 1, which left me with 0

**Note:** While this may sound overly complicated, it is actually very quick and easy to do when doing it on a piece of paper.

Now to find out the network address all we do is add the **SN** bits that have a 1 underneath them, together. (128 + 16 + 8 + 4 = 156).

When you add this 156 to the first three octets of the address, we're left with the **Network Address** 195.70.16.156.

Now, as we know that the **first usable address is always the Network Address plus one**, all we need to do is perform the following calculation: (156 + 1 = 157).

This gives us a **First Usable Address** of 195.70.16.157.

Now let's skip the Last Usable Address for a moment and find the Broadcast Address. To find out what it is, all we need to do is add all of the **H** bits together (regardless of whether they are a 1 or a 0) and then add this number to the Network Address. (2 + 1 + 156 = 159).

This gives us a **Broadcast Address** of 195.70.16.159.

And finally, let's work out the last usable address. This process is similar to finding the First Usable Address, however, instead of adding one to the network address, we actually subtract one from the Broadcast Address. (159 - 1 = 158).

This gives us a **Last Usable Address** of 195.70.16.158.

And there we have it! Our temaplte is complete. For easy reference, here it is again:

*   **Network Address:** 195.70.16.156
*   **First Usable Address:** 195.70.16.157
*   **Last Usable Address:** 195.70.16.158
*   **Broadcast Address:** 195.70.16.159

Other Posts in this Series:
---------------------------

See the **[Subnetting Made Easy - Index](/subnetting-made-easy-index/)** post for links to all of the posts in this series.