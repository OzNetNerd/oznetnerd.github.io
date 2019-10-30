---
title: 'Understanding MQC, Part 2: Basic Configuration'
sub_heading: "A series of posts to help you become a QoS expert!"
redirect_from: /understanding-mqc-part-2-basic-configuration/

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
[**In my previous post**](/understanding-mqc-part-1-basics/ "Understanding MQC, Part 1: The Basics") I mentioned the six steps which are required in order to implement a QoS solution. In this post I'll use those steps to create an example implementation. Before I do though, I'll first cover the **"priority"** and **"bandwidth"** commands.

## Priority & Bandwidth Commands

In Step 3 of the process you must assign a a portion of bandwidth to the Class Map. The two main options used here are **"priority"**and **"bandwidth"**. Details on what each of them do can be found [**here**](http://www.cisco.com/en/US/tech/tk543/tk757/technologies_tech_note09186a0080103eae.shtml).

In short though, the **"priority"** command is used for time sensitive applications where packets need to be sent ASAP, for example, VoIP calls. To ensure that these packets are sent as soon as possible, the router uses a special queuing process called Low Latency Queuing (LLQ) whereby all priority packets are sent immediately, even if there are other packets which on the router first.

The **"bandwidth"** command is used for non-time sensitive traffic, such as large file backups.

## Basic Configuration

Now that we've covered the basics, let's jump in to a Basic Configuration example using the six steps **[outlined in my previous post](/understanding-mqc-part-1-basics/ "Understanding MQC, Part 1: The Basics")**.

I'll be using iperf to generate traffic in my lab network. The two traffic flows I will prioritise are:

*   **Interface Shape:** 1mb
    *   **Bulk Data Traffic:**
        *   **Source IP:**  192.168.10.101
        *   **Destination IP:** 192.168.20.101
        *   **Destination Port:** 5001
        *   **DSCP Marking:** AF33
        *   **Bandwidth:** 300kb
    *   **Voice Traffic:**
        *   **Source IP: ** 192.168.10.101
        *   **Destination IP:** 192.168.20.101
        *   **Destination Port:** 5002
        *   **DSCP Marking:** EF
        *   **Priority:** 150kb

## Step 1 - Classify Class Map

**Note:** I'll be using ACLs to match the desired traffic, however, protocols can also be matched through the use of NBAR.

The "classify" ACLs look like this:

```
ip access-list extended BULK_DATA
  permit tcp host 192.168.10.101 host 192.168.20.101 eq 5001 
!
ip access-list extended VOICE
  permit tcp host 192.168.10.101 host 192.168.20.101 eq 5002
```

Now we need to create the "classify" Class Maps and tie them to the above ACLs:

```
class-map match-any  MARK-BulkData
  match access-group name BULK_DATA
!
class-map match-any  MARK-Voice
  match access-group name VOICE
```

## Step 2 - Inbound Policy Map

Next, we have to tie the above "classify" Class Maps to a Policy Map and assign the DSCP values to each of the Classes:

```
policy-map INGRESS-Mark-DSCP
  class MARK-BulkData
    set dscp af33 
!
  class MARK-Voice
    set dscp ef
```

Now we apply the Policy Map in the inbound direction on the router's LAN facing interface:

```
interface gi0/2.10
  desc LAN Port - Ingress Packet Marking
  service-policy input INGRESS-Mark-DSCP
```

## Step 3 - Prioritisation Class Map

**Note:** These Class Map names do not need to match the ones used in **Step 1**.

Here we match the DSCP values which were configured in **Step 2**:

```
class-map match-any MATCH-BulkData
  match dscp af33
 !
 class-map match-any MATCH-Voice
  match dscp ef
```

## Step 4 - Bandwidth Allocation Policy Map (Child)

Now we allocate the bandwidth to each of the above Class Maps using another Policy Map.  
Recall that this Policy Map will not be applied to an interface and will instead be applied underneath another Policy Map. That is why this Policy Map is known as a "Child" Policy Map:

```
policy-map CHILD-EGRESS-BandwidthAllocation
 class MATCH-BulkData
   bandwidth 300
 !
 class MATCH-Voice
   priority 150
```

## Step 5 & 6 - Shaper Policy Map (Parent)

Here we create the Parent Policy Map as well as apply the 1mb shaper. We then attach the Child Policy Map to it.

```
 policy-map  PARENT-EGRESS-Shaper
   class class-default
     shape average 1024000
     service-policy CHILD-EGRESS-BandwidthAllocation
```

Now we apply the Parent Policy Mapoutbound on the router's WAN facing interface:

```
interface gi 0/1
  service-policy output PARENT-EGRESS-Shaper
```

## Class-Default

Before I end this post I thought I should quickly mentioned the "Class-Default" class. Anything which does not match a manually configured Class (which in this example are the **MATCH-BulkData** and **MATCH-Voice** classes) will automatically be sent to the "Class-Default" class. The same goes for unallocated bandwidth. Both of these scenarios are discussed **[in my next post](/understanding-mqc-part-3-output-analysis/)**.