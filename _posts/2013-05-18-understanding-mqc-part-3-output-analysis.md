---
title: 'Understanding MQC, Part 3: Output Analysis'
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /understanding-mqc-part-3-output-analysis/

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
- Shaping
---
[**In my previous post**](/understanding-mqc-part-2-basic-configuration/ "Understanding MQC, Part 2: Basic Configuration") I implemented a basic QoS configuration. In this post I will demonstrate how the **"bandwidth"** and **"priority"** commands treat their respective classes, as well as how to interpret the **"show policy-map interface"** command output.

## Parent Shaper

Below is the output seen when the **"show policy-map interface"** command is issued. The Parent Policy's class-default Class Map (at the top of the output seen below) represents all of the Child Class Maps' counters put together.

For example, if 5 packets are dropped from the **"MATCH-Voice"** Class Map and 10 packets are dropped from the **"MATCH-BulkData"** Class Map, 15 drops will be shown under the Parent Policy.

```
Router#sh policy-map int gi 0/1
 GigabitEthernet0/1
  Service-policy output: PARENT-EGRESS-Shaper
    Class-map: class-default (match-any)
      1 packets, 68 bytes
      30 second offered rate 0 bps, drop rate 0 bps
      Match: any 
      Queueing
      queue limit 64 packets
      (queue depth/total drops/no-buffer drops) 0/0/0
      (pkts output/bytes output) 2/158
      shape (average) cir 1024000, bc 4096, be 4096
      target shape rate 1024000
      Service-policy : CHILD-EGRESS-BandwidthAllocation
        queue stats for all priority classes:
          Queueing
          queue limit 64 packets
          (queue depth/total drops/no-buffer drops) 0/0/0
          (pkts output/bytes output) 0/0
        Class-map: MATCH-BulkData (match-any)
          0 packets, 0 bytes
          30 second offered rate 0 bps, drop rate 0 bps
          Match:  dscp af33 (30)
            0 packets, 0 bytes
            30 second rate 0 bps
          Queueing
          queue limit 64 packets
          (queue depth/total drops/no-buffer drops) 0/0/0
          (pkts output/bytes output) 0/0
          bandwidth 300 kbps
        Class-map: MATCH-Voice (match-any)
          0 packets, 0 bytes
          30 second offered rate 0 bps, drop rate 0 bps
          Match:  dscp ef (46)
            0 packets, 0 bytes
            30 second rate 0 bps
          Priority: 150 kbps, burst bytes 3750, b/w exceed drops: 0

        Class-map: class-default (match-any)
          1 packets, 68 bytes
          30 second offered rate 0 bps, drop rate 0 bps
          Match: any 
          
          queue limit 64 packets
          (queue depth/total drops/no-buffer drops) 0/0/0
          (pkts output/bytes output) 1/98
```

## Offered & Dropped Rate

The "Offered Rate" and "Dropped Rate" figures are important figures to take note of. It gives you an average of how many bits per second each class has Class has sent and dropped respectively for an interval (the default interval is 5 minutes, however, I have changed it to 30 seconds using the **"load-interval 30"** command).

As demonstrated below, if a Class or multiple Classes do not use their allocated bandwidth, their "Offered Rate" will be less than their allocated bandwidth. This unused bandwidth can then be used by other Classes who require it. Due to this there will be times where you will see a Class' "Offered Rate" being higher than their allocated bandwidth.

**Note:** It is important to keep in mind that the "Offered" and "Dropped" rates are an average. This means that if you're using a 5 minute interval and there is a traffic one minute traffic spike, it is very likely that you won't see it in the "Offered" and "Dropped" rate figures.

## Bandwidth command

This demonstration shows how the router treats the "Bulk Data" traffic when there is no load coming from any other sources.

**iperf test:**

```
 [1912] local 192.168.10.101 port 1265 connected with 192.168.20.101 port 5001
 [ ID] Interval       Transfer     Bandwidth
 [1912]  0.0- 1.0 sec   128 KBytes  1.05 Mbits/sec
 [1912]  1.0- 2.0 sec   120 KBytes   983 Kbits/sec
 [1912]  2.0- 3.0 sec   128 KBytes  1.05 Mbits/sec
 [1912]  3.0- 4.0 sec   120 KBytes   983 Kbits/sec
 [1912]  4.0- 5.0 sec   120 KBytes   983 Kbits/sec
 [1912]  5.0- 6.0 sec   120 KBytes   983 Kbits/sec
 [1912]  6.0- 7.0 sec   120 KBytes   983 Kbits/sec
 [1912]  7.0- 8.0 sec   120 KBytes   983 Kbits/sec
 [1912]  8.0- 9.0 sec   120 KBytes   983 Kbits/sec
 [1912]  9.0-10.0 sec   120 KBytes   983 Kbits/sec
 [1912] 10.0-11.0 sec   120 KBytes   983 Kbits/sec
 [1912] 11.0-12.0 sec   120 KBytes   983 Kbits/sec
 [1912] 12.0-13.0 sec   120 KBytes   983 Kbits/sec
 [1912] 13.0-14.0 sec   120 KBytes   983 Kbits/sec
 [1912] 14.0-15.0 sec   120 KBytes   983 Kbits/sec
 [1912]  0.0-15.2 sec  1.78 MBytes   984 Kbits/sec
 ```

**Policy Map output:**

```
Router(config-if)#do sh policy-map int gi 0/1
GigabitEthernet0/1

Service-policy output: PARENT-EGRESS-Shaper

Class-map: class-default (match-any)
1494 packets, 1970729 bytes
30 second offered rate 118000 bps, drop rate 0 bps
Match: any
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 1533/1980890
shape (average) cir 1024000, bc 4096, be 4096
target shape rate 1024000

Service-policy : CHILD-EGRESS-BandwidthAllocation

queue stats for all priority classes:
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 0/0

Class-map: MATCH-BulkData (match-any)
1372 packets, 1941896 bytes
30 second offered rate 114000 bps, drop rate 0 bps
Match:  dscp af33 (30)
1372 packets, 1941896 bytes
30 second rate 114000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 1372/1941896
bandwidth 300 kbps

Class-map: MATCH-Voice (match-any)
0 packets, 0 bytes
30 second offered rate 0 bps, drop rate 0 bps
Match:  dscp ef (46)
0 packets, 0 bytes
30 second rate 0 bps
Priority: 150 kbps, burst bytes 3750, b/w exceed drops: 0
Class-map: class-default (match-any)
122 packets, 28833 bytes
30 second offered rate 4000 bps, drop rate 0 bps
Match: any

queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 122/28787
```

## Analysis

Looking at the **iperf test**, we can see that average speed achieved as 984kb/sec. This may seem strange seeing as though we specifically assigned 300kb to this class. However, as mentioned above, MQC Classes are allowed to use other Classes' bandwidth if it is not being used.

Looking at the MATCH-BulkData section of the router output, we can confirm that the packets are being matched and prioritised correctly.

## Priority command

This demonstration shows how the router treats the "Voice" traffic when there is no load coming from any other sources.

**iperf test:**

```
 [1912] local 192.168.10.101 port 1266 connected with 192.168.20.101 port 5002
 [ ID] Interval       Transfer     Bandwidth
 [1912]  0.0- 1.0 sec   184 KBytes  1.51 Mbits/sec
 [1912]  1.0- 2.0 sec  0.00 Bytes  0.00 bits/sec
 [1912]  2.0- 3.0 sec   768 KBytes  6.29 Mbits/sec
 [1912]  3.0- 4.0 sec   136 KBytes  1.11 Mbits/sec
 [1912]  4.0- 5.0 sec  72.0 KBytes   590 Kbits/sec
 [1912]  5.0- 6.0 sec   144 KBytes  1.18 Mbits/sec
 [1912]  6.0- 7.0 sec   112 KBytes   918 Kbits/sec
 [1912]  7.0- 8.0 sec  80.0 KBytes   655 Kbits/sec
 [1912]  8.0- 9.0 sec   160 KBytes  1.31 Mbits/sec
 [1912]  9.0-10.0 sec  72.0 KBytes   590 Kbits/sec
 [1912] 10.0-11.0 sec  8.00 KBytes  65.5 Kbits/sec
 [1912] 11.0-12.0 sec  0.00 Bytes  0.00 bits/sec
 [1912] 12.0-13.0 sec  8.00 KBytes  65.5 Kbits/sec
 [1912] 13.0-14.0 sec  8.00 KBytes  65.5 Kbits/sec
 [1912] 14.0-15.0 sec  16.0 KBytes   131 Kbits/sec
 [1912] 15.0-16.0 sec  0.00 Bytes  0.00 bits/sec
 [1912]  0.0-16.9 sec  1.73 MBytes   862 Kbits/sec
```

## Policy Map output:

```
Router(config-if)#do sh policy-map int gi 0/1
GigabitEthernet0/1

Service-policy output: PARENT-EGRESS-Shaper

Class-map: class-default (match-any)
1555 packets, 2131519 bytes
30 second offered rate 61000 bps, drop rate 6000 bps
Match: any
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/138/0
(pkts output/bytes output) 1418/1927733
shape (average) cir 1024000, bc 4096, be 4096
target shape rate 1024000

Service-policy : CHILD-EGRESS-BandwidthAllocation

queue stats for all priority classes:
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 1298/1899284

Class-map: MATCH-BulkData (match-any)
0 packets, 0 bytes
30 second offered rate 0 bps, drop rate 0 bps
Match:  dscp af33 (30)
0 packets, 0 bytes
30 second rate 0 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 0/0
bandwidth 300 kbps

Class-map: MATCH-Voice (match-any)
1436 packets, 2103396 bytes
30 second offered rate 57000 bps, drop rate 4000 bps
Match:  dscp ef (46)
1436 packets, 2103396 bytes
30 second rate 57000 bps
Priority: 150 kbps, burst bytes 3750, b/w exceed drops: 138
Class-map: class-default (match-any)
119 packets, 28123 bytes
30 second offered rate 4000 bps, drop rate 0 bps
Match: any

queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 119/28351
```

## Analysis

Looking at the **iperf test**, we can see that average speed achieved as 862kb/sec. Like the "bandwidth" command, the "priority" command also allows the use of extra bandwidth should it be available.

Looking at both the **iperf test** and the **"MATCH-Voice"** outputs we can see that multiple packets were dropped. The reason why we see drops when performing the Voice test but not the Bulk Data test is due to the "priority" and "bandwidth" commands. While the "bandwidth" command allows packets to be queued and sent when there is bandwidth available, the "priority" command (which uses Low Latency Queuing (LLQ)) sends the data straight away or it drops it. This is because, despite its name, LLQ does not queue packets. The reason for this is simple. When using a time-sensitive application, queuing packets would introduce too much delay which would result in packets arriving too late to be used. The queuing would then cause subsequent packets to be queued too and therefore they would be delayed as well, meaning that the router will always be trying to catch up. Instead, by dropping packets instead of delaying them, subsequent packets will be able to be delivered on time.

Another interesting thing to note here is that as per the **"MATCH-Voice"** output above, 138 packets were dropped. If you look at the router's interface counters, you will see that the drops also appear there too:

**Interface output:**

```
Router(config-if)#do sh int gi0/1 | i drops
 Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 138
 Output queue: 0/1000/138 (size/max total/drops)
```

## Multiple Streams

When there are multiple streams of data (Voice traffic, Bulk Data traffic and Unclassified traffic) being sent simultaneously, the policy output looks like this:

```
Router(config-if)#do sh policy-map int gi 0/1
GigabitEthernet0/1

Service-policy output: PARENT-EGRESS-Shaper

Class-map: class-default (match-any)
6630 packets, 9284598 bytes
30 second offered rate 932000 bps, drop rate 39000 bps
Match: any
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 51/239/0
(pkts output/bytes output) 6413/8931702
shape (average) cir 1024000, bc 4096, be 4096
target shape rate 1024000

Service-policy : CHILD-EGRESS-BandwidthAllocation

queue stats for all priority classes:
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 963/1395762

Class-map: MATCH-BulkData (match-any)
1839 packets, 2606098 bytes
30 second offered rate 281000 bps, drop rate 0 bps
Match:  dscp af33 (30)
1839 packets, 2606098 bytes
30 second rate 281000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 17/0/0
(pkts output/bytes output) 1839/2606098
bandwidth 300 kbps

Class-map: MATCH-Voice (match-any)
1202 packets, 1754308 bytes
30 second offered rate 121000 bps, drop rate 38000 bps
Match:  dscp ef (46)
1202 packets, 1754308 bytes
30 second rate 121000 bps
Priority: 150 kbps, burst bytes 3750, b/w exceed drops: 239
Class-map: class-default (match-any)
3589 packets, 4924192 bytes
30 second offered rate 530000 bps, drop rate 0 bps
Match: any

queue limit 64 packets
(queue depth/total drops/no-buffer drops) 32/0/0
(pkts output/bytes output) 3589/4924428
```

## Analysis

When situations like this occur, you will find that each Class will have an "offered" rate which is very close to the the Class' allocated bandwidth amount. If one or more of the Classes drops below their allocated bandwidth amount you will see their "offered rate" drop, however, you will also see one or more of the other classes' "offered rate" rate increase. This is because, as demonstrated previously, Classes can consume more than their allocated bandwidth should it be available.

## Unallocated Bandwidth

All bandwidth which has not been allocated to a Class is given to the Child Policy's "class-default".  
Also, [**as mentioned in my previous post**](/understanding-mqc-part-2-basic-configuration/ "Understanding MQC, Part 2: Basic Configuration"), anything which does not match a manually configured Class (which in this example are the MATCH-BulkData and MATCH-Voice classes) will automatically be sent to the "Class-Default" class. In other words, unless traffic and bandwidth is specifically assigned to a Class, it will be assigned to the Class-Default class.  
To verify this, let's take another look at the **"show policy-map int gi 0/1"** output under the "Multiple Streams" above:

We can see that **"MATCH-BulkData"** and **"MATCH-Voice"** have an offered rate of 281000 bits (274kb) and 121000 bits (118kb) respectively which is just below their allocated bandwidths of 300kb and 150kb respectively. As we're using a 1mb shaper, this means that we should have about 574kb left over. Looking at the class-default class we can see that it has an offered rate of 530000 bits (517kb) - which is very close to the 574kb we were looking for.

## Sharing Unused Bandwidth

In the above sections I've talked about and demonstrated the fact that Classes can use more bandwidth than they're allocated if there is additional unused bandwidth available.What I have not explained yet though is how the router decides which classes to give the additional bandwidth to should more than one need it.

It is actually very simple - The router uses each Classes' original bandwidth configuration as a weight. The more bandwidth the Class has been configured with, the more "weight" it has, and therefore it will be allocated a larger amount of the unused bandwdith.

For example, above we used the **"MATCH-BulkData"** and **"MATCH-Voice"** classes, giving them 300kb and 150kb respectively. This means that MATCH-BulkData has twice the amount of "weight" that MATCH-Voice has, and therefore should be allocated twice has much unused bandwidth. Let's run a test and see if that is true:

```
Router(config-if)#do sh policy-map int gi 0/1
GigabitEthernet0/1

Service-policy output: PARENT-EGRESS-Shaper

Class-map: class-default (match-any)
6347 packets, 8861862 bytes
30 second offered rate 920000 bps, drop rate 0 bps
Match: any
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 33/0/0
(pkts output/bytes output) 6348/8862106
shape (average) cir 1024000, bc 4096, be 4096
target shape rate 1024000

Service-policy : CHILD-EGRESS-BandwidthAllocation

Class-map: MATCH-BulkData (match-any)
4136 packets, 5867664 bytes
30 second offered rate 610000 bps, drop rate 0 bps
Match:  dscp af33 (30)
4136 packets, 5867664 bytes
30 second rate 610000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 17/0/0
(pkts output/bytes output) 4136/5867664
bandwidth 300 kbps

Class-map: MATCH-Voice (match-any)
2084 packets, 2955204 bytes
30 second offered rate 307000 bps, drop rate 0 bps
Match:  dscp ef (46)
2084 packets, 2955204 bytes
30 second rate 307000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 14/0/0
(pkts output/bytes output) 2084/2955204
bandwidth 150 kbps

Class-map: class-default (match-any)
127 packets, 38994 bytes
30 second offered rate 6000 bps, drop rate 0 bps
Match: any

queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 127/39178
```

As we can see above, **"MATCH-BulkData"** has an Offered Rate of 610000 bits (310000 additional bits taken from the unused "Class-Default" class). We can also see that  **"MATCH-Voice"** has an Offered Rate of 307000 bits (157000 additional bits taken from the unused "Class-Default" class). This confirms that **"MATCH-BulkData"** was allocated very close to double the amount of bits which **"MATCH-Voice"** was given.

**Note:** You may have noticed that I changed **"MATCH-Voice"** to a "bandwidth 150" in the last output above. The reason for this is because as shown above, using the **"priority"** command with iperf results in quite a lot of packet loss, therefore it would affect the "Offered Rate" shown.

Below is another test which demonstrates the splitting of unused bandwidth. **"MATCH-BulkData"** again uses the **"bandwidth 300"** statement but this time **"MATCH-Voice"** has used the **"bandwidth 100"** command. This therefore means that **"MATCH-BulkData"** should be allocated three times as much unused bandwidth as compared to **"MATCH-Voice"**:

```
BO1-RTR-2951(config-pmap-c)#do sh policy-map int gi 0/1
GigabitEthernet0/1

Service-policy output: PARENT-EGRESS-Shaper

Class-map: class-default (match-any)
6267 packets, 8720258 bytes
30 second offered rate 897000 bps, drop rate 0 bps
Match: any
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 35/0/0
(pkts output/bytes output) 6288/8725874
shape (average) cir 1024000, bc 4096, be 4096
target shape rate 1024000

Service-policy : CHILD-EGRESS-BandwidthAllocation

Class-map: MATCH-BulkData (match-any)
4580 packets, 6497848 bytes
30 second offered rate 669000 bps, drop rate 0 bps
Match:  dscp af33 (30)
4580 packets, 6497848 bytes
30 second rate 669000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 17/0/0
(pkts output/bytes output) 4580/6497848
bandwidth 300 kbps

Class-map: MATCH-Voice (match-any)
1544 packets, 2188764 bytes
30 second offered rate 226000 bps, drop rate 0 bps
Match:  dscp ef (46)
1544 packets, 2188764 bytes
30 second rate 226000 bps
Queueing
queue limit 64 packets
(queue depth/total drops/no-buffer drops) 16/0/0
(pkts output/bytes output) 1544/2188764
bandwidth 100 kbps

Class-map: class-default (match-any)
143 packets, 33646 bytes
30 second offered rate 4000 bps, drop rate 0 bps
Match: any

queue limit 64 packets
(queue depth/total drops/no-buffer drops) 0/0/0
(pkts output/bytes output) 143/33908
```

As we can see above, **"MATCH-BulkData"** has an Offered Rate of 669000 bits and **"MATCH-Voice"** has an Offered Rate of 226000 bits. This confirms that **"MATCH-BulkData"** was allocated very close to three times the amount of bits which **"MATCH-Voice"** was given.