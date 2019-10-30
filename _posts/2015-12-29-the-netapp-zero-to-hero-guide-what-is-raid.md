---
title: "The NetApp Zero to Hero Guide - What is RAID?"
sub_heading: "Get more out of your storage"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

# categories:
#   - TBA

# tags:
#   - TBA
---

If you've never delved into the server or storage side of the IT industry, there is a chance you might not have heard of RAID before (Redundant Array of Independent Disks). Now before I get started I just wanted to point out that as there is already a massive amount of RAID information available on the internet, I'm going to do my best not to reinvent the wheel. Speaking of which, I'll begin with a quote from Wikipedia: _"RAID (originally redundant array of inexpensive disks, now commonly redundant array of independent disks) is a data storage virtualization technology that **combines multiple physical disk drive** components into a **single logical unit** for the purposes of **data redundancy, performance improvement, or both.**_ _"Data is distributed across the drives in one of several ways, referred to as RAID levels, depending on the required level of redundancy and performance. The different schemas, or data distribution layouts, are named by the word RAID followed by a number, for example RAID 0 or RAID 1. Each schema, or a RAID level, provides a different balance among the key goals: **reliability, availability, performance, and capacity.** RAID levels greater than RAID 0 provide protection against unrecoverable sector read errors, as well as against failures of whole physical drives."_ To summarise the information I have highlighted in bold above:

*   RAID makes multiple physical disks appear as one logical disk.
*   It does this for redundancy and/or performance improvements.
*   There are multiple levels of RAID.
*   The main goals of RAID are reliability, availability, performance and capacity.

This is all great information, but the last point really hits the nail on the head in regards to why RAID is a must when we're talking about storage for an application/server and downtime is not an option.

## Picture It

As they say, a picture is worth a thousand words, so here it is.

*   The **"A"** diagram is what you'll see in your standard home grade PC or laptop - a single hard drive or two connected to the PC with no smarts to it. If the hard drive fails, you lose the data.
*   The **"B"** diagram is what you'll see if you've got a server with a RAID controller and a few hard drives in it. Using this method allows you to take advantage of all the goodies RAID has to offer.
*   The **"C"** diagram is what you'll see if you've got a NAS/SAN solution

[![RAID](/assets/2015/12/RAID.png)](/assets/2015/12/RAID.png)

## Which RAID Level Should I Use?

The answer is, it depends. (Don't you just hate it when people say that?!?) If you're using a NetApp FAS system then the recommendation is to use RAID DP. But don't worry if you're not, there are plenty of resources **[(such as this one)](http://www.techrepublic.com/blog/the-enterprise-cloud/choose-a-raid-level-that-works-for-you/)** which can help you make the right decision. The best advice I can give you in regards to choosing a RAID level is to make sure you have a deep understanding of your application and its needs, as well as the needs of your business. Do not rush this decision as it could end up being a very costly mistake.