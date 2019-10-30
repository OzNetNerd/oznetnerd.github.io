---
title: Shape Average Vs Shape Peak - Part 3
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /shape-average-vs-shape-peak-part-3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Labs
- QoS

tags:
- QoS
- GNS3
- Labs
- Cisco
- Useful Commands
- Shaping
---
In my [**previous post**](/shape-average-vs-shape-peak-part-2/ "Shape Average Vs Shape Peak – Part 2") in this series I covered the difference between Shape Average, Shape Peak and Shape with no Excess. Now that that's out of the way, let's get down to configuration examples. I'll use similar specifications to the ones I used last time:

*   **CIR** = 512kbps (512,000 bps)
*   **Bc** = 5,120 bps
*   **Tc** = 10ms (0.001 seconds)
*   **Be** = 5,120 bps for Shape Average and Shape Peak. (Shaping with no Excess will be covered in my next post).

##  Shape Average

Below is a basic Shape Average policy map with a 512kb shaper applied.

```
R1(config)#policy-map ShapeAverage-512k
R1(config-pmap)# class class-default
R1(config-pmap-c)#shape average 512000
R1(config-pmap-c)#interface fa0/0
R1(config-if)#service-policy output ShapeAverage-512k
R1(config-if)#do sh policy-map int f0/0

 FastEthernet0/0

  Service-policy output: ShapeAverage-512k

    Class-map: class-default (match-any)
      0 packets, 0 bytes
      5 minute offered rate 0 bps, drop rate 0 bps
      Match: any
      Traffic Shaping
           Target/Average   Byte   Sustain   Excess    Interval  Increment
             Rate           Limit  bits/int  bits/int  (ms)      (bytes)
           512000/512000    3200   12800     12800     25        1600

        Adapt  Queue     Packets   Bytes     Packets   Bytes     Shaping
        Active Depth                         Delayed   Delayed   Active
        -      0         0         0         0         0         no
```

##  Analysis

The first thing to note is that I did not have to configure the **Bc** and **Be** values myself, the router did it for me. In fact, [**as mentioned in my previous post**](/shape-average-vs-shape-peak-part-2/ "Shape Average Vs Shape Peak – Part 2"), Cisco do not recommend choosing these values manually.

The next thing to note is the interval. As there is no configuration command which allows me to manually set the **Tc**, it would appear that I'm stuck using the router's default 25ms. This therefore means that our **Bc** and **Be** figures are incorrect too. (More on this in my next post).

The final thing to note is the two parts of the "show" outputs which I underlined. The "Rate" and the "Interval". The Shape Average policy map has a Target/Average of 512,000/512,000. This is because as [**described previously**](/shape-average-vs-shape-peak-part-2/ "Shape Average Vs Shape Peak – Part 2"), **unused** Shape Average tokens are put in to the Be bucket for use by future Tc intervals. This allows the router to still reach (but not exceed) its 512k/sec even when it encounters Tc intervals which do not use all of the Bc tokens.

On the other hand the Shape Peak policy map has a Target/Average of 1,024,000/512,000. This is because (as also described previously), Shape Peak allows the router to use **Bc + Be** every interval, regardless of whether there were unused **Bc** tokens in the previous **Tc** or not. Therefore, as **Be = Bc** by default, this allows the router to send traffic up to 1mb/sec.

Configuring a shaper with no excess burst requires manually setting the **Bc** and **Be** values so it will be covered in my next post, as will changing the Tc interval.

**Note: **See the [**Understanding MQC Series**](/understanding-mqc-series/ "Understanding MQC Series") for more information.