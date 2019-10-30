---
title: Subnetting Made Easy, Part 2
sub_heading: "A series of posts to help you become a subnetting expert!"
redirect_from: /subnetting-made-easy-part-2/

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
In my previous post, **[Subnetting Made Easy, Part 1](/subnetting-made-easy-part-1/ "Subnetting Made Easy, Part 1")**, I demonstrated the way I use to find the Network Address, First Usable Address, Last Usable Address and Broadcast Address when given any IP address and subnet mask. This time I will demonstrate how this process can also be used when dealing with multiple, consecutive subnets.

To make things easier, I'll use the same setup as last time.

As per [**Part 1**](/subnetting-made-easy-part-1/ "Subnetting Made Easy, Part 1"), we found that the network address was 195.70.16.156. We also knew that we were only going to be dealing with the fourth octet as this is a /30 address. We then converted this information in to binary format and were left with this:

|||||||||
|--- |--- |--- |--- |--- |--- |--- |--- |
|128|64|32|16|8|4|2|1|
|SN|SN|SN|SN|SN|SN|H|H|
|1|0|0|1|1|1|1|1|

Now, let's get started on the next part. To put a clear division between the Subnet Bits and the Host Bits, what I like to do is put a line between them, like this:

||||||||||
|--- |--- |--- |--- |--- |--- |--- |--- |--- |
|128|64|32|16|8|4|||2|1|
|SN|SN|SN|SN|SN|SN|||H|H|
|1|0|0|1|1|1|||1|1|

Now we can clearly see that the last Subnet Bit is **4**. This instantly tells us that if we want to create additional, identical subnets, all of the Network Addresses are going to be 4 apart. To help me better explain what I mean, I'll use the below table.

**Legend:**

*   N = Network Address
*   First = First Usbale Address
*   Last = Last Usable Address
*   B/C = Broadcast Address

|||||
|--- |--- |--- |--- |
|N|First|Last|B/C|
|||||
|||||

Once again, looking at the [**previous entry**](/subnetting-made-easy-part-1/ "Subnetting Made Easy, Part 1"), we found the following information:

*   **Network Address:** 195.70.16.156
*   **First Usable Address:** 195.70.16.157
*   **Last Usable Address:** 195.70.16.158
*   **Broadcast Address:** 195.70.16.159

When inserted in to the table, it looks like this:

|||||
|--- |--- |--- |--- |
|N|First|Last|B/C|
|156|157|158|159|
|||||

Now, as mentioned above, we know that the Subnets are going to be 4 bits apart. This means all we need to do is add 4 to the current network address. e.g 156 + 4 = 160. Then, to get the next network address after that, we'd do 4 + 160 = 164. And so on. Using this forumula, the table now looks like this:

|||||
|--- |--- |--- |--- |
|N|First|Last|B/C|
|156|157|158|159|
|160||||
|164||||
|168||||

Now all we need to do is some more basic maths. Using the following formulas, we can fill in the rest of the table:

*   **First Address** = Network Address + 1
*   **Broadcast Address** = Next Network Address - 1
*   **Last Address** = Broadcast Address - 1

There are two things to note here. As you can see, the Last Address formula requires that you first find out what the Broadcast Address is.

The second thing to note is that the "Next Network Address" that the Broadcast Address formula is referring to is network address of the following subnet. For example, as per the table above, if we were working on the network address 195.70.16.160, we know the next network address is 195.70.16.164.

Speaking of which, let's do that now.

Using the above formulas and the 195.70.16.160 Network Address, this is what we end up with:

*   **First Address** = 160 + 1 = 161
*   **Broadcast Address** = 164 - 1 = 163
*   **Last Address** = 163 - 1 = 162

|||||
|--- |--- |--- |--- |
|N|First|Last|B/C|
|156|157|158|159|
|160|161|162|163|
|164||||
|168||||

Then if we do the same with for the 195.70.16.164 subnet, this is what we'd end up with:

|||||
|--- |--- |--- |--- |
|N|First|Last|B/C|
|156|157|158|159|
|160|161|162|163|
|164|165|166|167|
|168||||

I'll leave the 195.70.16.168 network blank so that you can have a try yourself.

**Note:** You may notice that all of the numbers are simply four higher than one another each time you go down a row. It is not advisable to just keep adding four to the numbers because while it may work in this case, if you were dealing with different subnet masks it wouldn't, however, the formula still would.

###  Other Posts in this Series:

See the **[Subnetting Made Easy - Index](/subnetting-made-easy-index/)** post for links to all of the posts in this series.