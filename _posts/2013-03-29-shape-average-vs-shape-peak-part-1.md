---
title: Shape Average Vs Shape Peak - Part 1
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /shape-average-vs-shape-peak-part-1/

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
When I first started looking in to the differences between Shape Average and Shape Peak, I soon found myself getting lost in all of the acronyms - Tc, Bc, Be, CIR, PIR, and so on. However, after some research and labbing, it all became much clearer. I plan to put together my notes and lab tests in a series of blog posts in the hope that they will assist others in understanding how it works.

Note, as this is quite a complex topic I will start with a high level overview to assist readers in getting their minds around the basics. The future posts will delve deeper in to the details.

## Syntax

Below is the syntax of the command. Each option will be described below.

```
shape {average | peak} cir [bc] [be]
```

## Definitions

Below is a list of the acronyms which are used in shaping.

*   **Tc** – Time interval (in milliseconds).
*   **Bc** – Committed burst size (in bits). This is the amount of traffic that can be sent per interval (**Tc**).
*   **Be** -  Excess burst size (in bits). A burstable rate which can be sent in conjunction with the **Bc** to achieve greater speeds per interval (**Tc**).
*   **CIR** – Committed Information Rate (in bits per second). The **guaranteed** rate of traffic which can be sent per second, as agreed on the traffic contract with your ISP.
*   **PIR** – Peak Information Rate (in bits per second). This figure represents Bc + Be.

## Bc and CIR Relationship

It may not be completely clear in the descriptions above, but the **Bc** and the **CIR** are directly related. As mentioned above, the **CIR** defines how much traffic can be sent per second. The **Bc**, on the other hand, defines how much of the **CIR's** data can be sent per sub-second. In other words, the **Bc** gives you granular control over the **CIR**. This is explained further below.

## Token Buckets

Shaping's entire workings are based on Token Buckets.

One token represents a single bit of data. When you configure the shape, you define how many tokens (bits) you'd like the router to send per second. This is known as the CIR. As mentioned above, this is the _"**guaranteed** rate of traffic which can be sent per second, as agreed on the traffic contract with your ISP"_. So if you bought a 500kb/sec service from your ISP, your CIR would be 500,000 bps. This therefore means the router will put 500,000 tokens in its Token Bucket every second.

To give you more control over how these tokens are used, you can define how many tokens are put in to the Token Bucket per **sub-second** by defining the **Bc**. (More on this later).

To ensure that the router does not use more data than is permitted by your traffic contract, it will remove a single token for every bit of traffic it sends.

**Note:** The size of the Token Bucket is equal to the size of the **Bc**.

## Intervals

Recall above I mentioned that the **CIR** defines how much traffic can be sent in a second, and that the **Bc** defines how much of the **CIR** can be sent per sub-second. The way in which the **Bc** works is by using the **Tc**.

The **Tc** splits one second in to multiple sub-seconds (measured in milliseconds). As we're now talking about sub-seconds the **CIR** (which is measures in seconds) cannot be directly aligned to the **Tc**. To resolve this, we talk about the Bc instead. This is because the **Bc** represents the **CIR** and is measured in milliseconds, therefore it can be aligned directly to the **Tc**.

## Example

Let's say for example that your **Tc** intervals are 10 **milliseconds** each. This means you'll have **100** Tc intervals per second. (1 second divided by 0.01 seconds = 100).

Now let's say you have a **CIR** of 500,000 bits per **second**. As we can see, it's not going to be easy to align the two figures as one is measured in milliseconds and the other is measured in seconds. This is where the **Bc** comes in to play.

By dividing the **CIR** of 500,000 by the 100 intervals, we find that the **Bc** is 5,000 bits. Therefore, we now know we can send 5,000 bits per interval (in other words, 5,000 bits per 10 milliseconds). To think of it another way would be to say that there are 5,000 tokens put in to the Token Bucket every interval/10 milliseconds.

During one of the 100 intervals, if the router were to send 5,000 bits in less than 10 milliseconds (e.g 7 milliseconds), its Token Bucket would then be empty, therefore it would have to wait 3 milliseconds for the next interval at which time the Token Bucket would be re-filled with an additional 5,000 tokens.

You may ask yourself, what if the reverse happens? What if after an interval (10 milliseconds) only 3,000 bits were sent - what happens to the remaining 2,000 tokens in the Token Bucket? The answer to this question depends on whether you use Shaping with no Excess Burst, Shape Average or Shape Peak, all of which will be covered in my next blog post.

## Be

The final acronym I will discuss in this post is **Be**.

**Be** is only used when you implement Shape Average or Shape Peak. As you might have guessed, it is not used when Shaping with no Excess Burst is used, because **Be** actually stands for Excess Burst.

In short, **Be** allows you to increase the size of your Token Bucket and/or increase the amount of tokens put in the bucket. This is done to ensure that you can meet the **CIR** (where Shape Average is concerned) or to use more throughput than is specified by the **CIR** (where Shape Peak is concerned).

The last thing I'll mention in this post about the **Be** is that if you do not specifically configure it, the router will automatically make it equal to the **Bc**. This means that if your **Bc** is 5,000, your **Be** will also be 5,000.

## Update

Please see **[Part 2](/shape-average-vs-shape-peak-part-2/ "Shape Average Vs Shape Peak – Part 2") **of this series, as well as the [**Understanding MQC Series**](/understanding-mqc-series/ "Understanding MQC Series") for more information.