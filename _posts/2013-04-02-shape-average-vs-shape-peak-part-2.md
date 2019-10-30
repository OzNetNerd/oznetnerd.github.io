---
title: Shape Average Vs Shape Peak - Part 2
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /shape-average-vs-shape-peak-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- QoS

tags:
- QoS
- Cisco
- Shaping
---
**Note:** If you haven't read [**Shape Average Vs Shape Peak - Part 1**](/shape-average-vs-shape-peak-part-1/ "Shape Average Vs Shape Peak – Part 1") already, I suggest you read it first and then return to this entry.

## Three Types of Shaping

There are three types of shaping, and they are called:

*   Shape Average
*   Shape Peak
*   Shaping with no Excess

Using the following details, I will explain how each shaping type answers the following question - What if after an interval (10 milliseconds) only 3,00 bits are sent - _what happens to the remaining 2,000 tokens in the Token Bucket?_

*   **CIR** = 500kbps (500,000 bps)
*   **Bc** = 5,000 bps
*   **Tc** = 10ms (0.001 seconds)
*   **Be** = 5,000 bps for Shape Average and Shape Peak, 0 bps for Shaping with no Excess

Before reading on, please keep the following information in mind:

*   When using the above details, there are 100 intervals in 1 second
*   The Token Bucket size = **Bc** - Therefore in this case the Token Bucket can hold no more than 5,000 tokens
*   Each Tc**,** the router will add 5,000 tokens to its bucket - the way in which the router handles unused tokens is dependent on which shaping method is used
*   When using Shape Average and Shape Peak, if you do not specify the Be, it will automatically be the same size as the **Bc**. Therefore in this examples below, the **Be** will be 5,000 for Shape Average and Shape Peak.

## Shape Average

As per the question above, we want to know how Shape Average handles the 2,000 unused tokens at the end of the **Tc**.

Instead of discarding those left over tokens, Shape Average will put them in to the **Be** bucket. By doing this, future **Tc** intervals will be able to utilise these tokens, and therefore you will still be able to meet your **CIR**.  
For example, when the next **Tc** comes the **Bc** bucket will be filled with 5,000 new tokens as it is every other **Tc**. However, the router will also have access to the additional 2,000 tokens which were put in to the **Be** bucket - therefore the router is able to send 7,000 bits this **Tc** as opposed to 5,000 bits as is the regular amount. So you see, even though the router did not fully utilise its previous **Bc**, it is able to make up for it in a subsequent **Tc** and therefore it will still be able to fully utilise its **CIR**.

## Shape Peak

The way in which Shape Peak uses the **Be** is completely different to the way Shape Average does. In the above explanation we saw that Shape Average uses **Be** to keep **unused** Bc tokens for later use so that the **CIR** can be met. Shape Peak on the other hand fills **both** the **Bc** and **Be** buckets with tokens every **Tc**. This means that instead of the router being able to use 5,000 tokens per interval, it can now use 10,000 tokens per interval. This equates to 10,000 bits per interval which equates to 1mb per second.

In regards to the question, what happens to the 2,000 unused tokens - the answer is, they're discarded and replaced with 10,000 new tokens.

## Why the Extra Tokens?

You might be wondering why Shape Peak fills both the **Bc** and **Be** buckets with tokens every interval. The reason is because ISPs can allow customers to send extra bursts of traffic over and above what has been agreed to in their traffic contract (also known as the **CIR**). However, this extra allowance is not guaranteed and is therefore susceptible to being dropped when the ISP's network is congested.

The reason why ISPs offer this solution is because it works out more cost effective for them and their customers. This is because it would be too expensive for them to offer all of their customers a guaranteed 1mb connection as they know that most of the customers won't be using the full bandwidth consistently. Therefore to save on costs they will instead give each customer a guaranteed 500kb connection (**Bc**) and will also give them an additional, non-guaranteed 500kb (Be), resulting in a 1mb/sec connection when there is no congestion on the network.  
In short, by using the **Be** users can send more data than is specified by their **CIR**, but they must be prepared to see packet loss during times of congestion on their ISPs network.

## Shaping with no Excess

As mentioned in my previous post, Shaping with no Excess does not use **Be**. Because of this, unused tokens are not able to be set aside and used in a later **Tc** as is the case with Shape Average and are therefore discarded. This means that if tokens are unused during a **Tc**, you will not be able to meet the CIR for that one second as all subsequent intervals will still only be able to use 5,000 tokens each.

## Changing Values

On most platforms, you cannot _directly_ change the **Tc** value. Further to this, Cisco recommends that you do not change the **Bc** and **Be** values manually and instead let the router choose them for you. However, rules were made to be broken! Engineers may find that they need to change their **Tc** value to ensure time sensitive packets are sent in a timely manner. They may also want to change the **Be** to a value other than one which is equal to **Bc** (which is the default). Both of these scenarios will be covered in a future post.

## Update

Please see [**Part 3**](/shape-average-vs-shape-peak-part-3/ "Shape Average Vs Shape Peak – Part 3") of this series, as well as the [**Understanding MQC Series**](/understanding-mqc-series/ "Understanding MQC Series") for more information.