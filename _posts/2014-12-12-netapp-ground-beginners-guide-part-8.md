---
title: NetApp From the Ground Up - A Beginner's Guide Part 8
sub_heading: "A series of posts to help you become a storage expert!"
redirect_from: /netapp-ground-beginners-guide-part-8/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Beginner's Guide

tags:
- Netapp Newbies
- NetApp
---
## HA Pair
###  Summary

*   **Reference: [Overview of NetApp Replication and HA features](http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/ "http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/")**

HA Pair is basically two controllers which both have connection to their own and partner shelves. When one of the **controllers** fails, the other one takes over. It’s called **Cluster Failover (CFO)**. Controller NVRAMs are mirrored over NVRAM interconnect link. So even the data which hasn’t been committed to disks isn’t lost.

**Note:** HA Pair **can’t** failover when disk shelf fails, because partner doesn't have a copy to service requests from.

## Mirrored HA Pair
###  Summary

*   **Reference: [Overview of NetApp Replication and HA features](http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/ "http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/")**

You can think of a Mirrored HA Pair as **HA Pair** with **SyncMirror** between the systems. You can implement **almost** the same configuration on HA pair with SyncMirror inside **(not between)** each system (because the odds of the whole storage system (controller + shelves) going down is **highly unlikely**). But it can give you more peace of mind if it’s mirrored **between** two system.

It **_cannot_** failover like **MetroCluster**, when one of the storage systems goes down. The whole process is **manual**. The reasonable question here is why it cannot failover if it has a copy of all the data? Because MetroCluster is a **separate functionality**, which performs all the checks and carry out a cutover to a mirror. It’s called **Cluster Failover on Disaster (CFOD)**. SyncMirror is only a **mirroring** facility and **doesn't** even know that cluster exists.

## MetroCluster
### Summary

*   **Reference: [Overview of NetApp Replication and HA features](http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/ "http://niktips.wordpress.com/2013/08/09/overview-of-netapp-replication-and-ha-features/")**

MetroCluster provides failover on a **storage system** level. It uses the same **SyncMirror** feature beneath it to mirror data between two storage systems (instead of two shelves of the same system as in pure **SyncMirror** implementation). Now even if a storage **controller** fails together with all of its storage, you are **safe**. The other system takes over and continues to service requests.

**HA Pair can’t** failover when disk shelf fails, because partner doesn’t have a copy to service requests from.

### Information

*   **Reference: NetApp Training - Fast Track 101: NetApp Portfolio**

After Disaster Recovery, let's consider Continuous Availability. Customers must be able to recover critical applications after a system failure **seamlessly** and **instantaneously**. Critical applications include financial applications and manufacturing operations, which must be continuously available with near-zero RTO and zero RPO. NetApp MetroCluster is a unique **array-based clustering** solution that can extend up to **200km** and enable a zero RPO (no data loss) with zero or near-zero RTO.

MetroCluster enhances the built-in redundancy of NetApp hardware and software, providing an additional layer of protection for the entire storage and host environment. MetroCluster seamlessly maintains application and virtual infrastructure availability when storage outages occur (whether the outage is due to a network connectivity issue, loss of power, a problem with cooling systems, a storage array shutdown, or an operational error). Most MetroCluster customers report that their users experience no application interruption when a cluster recovery occurs.

MetroCluster enables **single command** fail over for seamless cutover of applications that is transparent to the end user.

It supports a distance of up to **100 kilometers.**

## Integrated Data Protection
### Information

*   **Reference: NetApp Training - Fast Track 101: NetApp Portfolio**

When discussing data protection strategies with your customers, you should consider the overall NetApp data protection portfolio, which we call NetApp Integrated Data Protection. NetApp Integrated Data Protection enables customers to:

*   Deliver backup 
*   High availability
*   Business continuity
*   Continuous availability

from a single platform. It is a **single suite** of integrated products that works across all NetApp solutions and with non NetApp storage. Your customers can use a single platform for all data protection, the process of building, implementing, and managing data protection over time is simpler, because they have fewer systems from fewer vendors to install and manage. And because the portfolio uses NetApp storage efficiency technology, cost and complexity can be up to 50% lower than for competitive solutions.

NetApp **Snapshot** copies are the answer to shrinking backup windows. They are nearly instantaneous, and as a result **do not** impact the application. As a result, multiple Snapshot copies can be made per day - hourly or even more often. They are the **primary solution** for protecting against user errors or data corruption.

**MetroCluster** is NetApp's solution for Continuous Availability, enabling **zero data loss** in the event of wide variety of failure scenarios. MetroCluster, in conjunction with VMware's HA and Fault Tolerance capabilities, give your customers **continuous availability** of their ESX servers and storage. For single failures, the storage systems will perform an automated, transparent failover. MetroCluster has been certified with VMware's High Availability and Fault Tolerant solutions.

**SnapMirror** provides asynchronous mirroring across unlimited distances to enable Disaster Recovery from a secondary site. SnapMirror, in conjunction with VMware's Site Recovery Manager, delivers automated, global failover of the entire virtual environment to a recovery site, and integrates with SnapMirror and **FlexClone**.

NetApp enables your customers to use less expensive SATA drives as nearline storage, and lower-cost controllers for asymmetric backups and backup consolidation from multiple sources. NetApp solutions enable rapid search and retrieval of backup data, and also support the re-use of backup data for other business uses via our unique, near-zero impact FlexClones.

NetApp enables your customers to perform flexible backup vaulting: Disk to Disk to Tape, full tape management as well as full cataloging of disk and tape backups. In addition, NetApp allows your customers to choose how they want to manage their data protection workflows. Your customers can use NetApp products such as **SnapProtect** for end-to-end backup management including catalog and disk-to-disk-to-tape; and for specific applications your customers can leverage the **SnapManager** software.

### Considering the Overall Data Protection Portfolio

*   **Reference: NetApp Training - Fast Track 101: NetApp Portfolio**

NetApp Integrated Data Protection enables customers to deliver high availability, business continuity, continuous availability, and backup and compliance from a **single platform.** A single platform that works across all NetApp solutions and with non NetApp storage. Because customers can use a single platform for all data protection, the process of building, implementing, and managing data protection over time is **simpler**, because they have **fewer systems** from fewer vendors to install and manage. And because the portfolio uses NetApp storage efficiency technology, cost and complexity can be up to **50% lower** than for competitive solutions. For example, a customer can use **MetroCluster** to provide a zero RPO and then replicate data with **SnapVault** software to a remote site for long-term backup and recovery. If the customer later decides to implement long-distance disaster recovery with a short RPO, the customer can use **SnapMirror** software to do so.

## Other Posts in this Series

See the [**NetApp From the Ground Up - A Beginner's Guide - Index**](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.