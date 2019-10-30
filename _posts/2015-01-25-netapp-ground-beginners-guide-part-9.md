---
title: NetApp From the Ground Up - A Beginner's Guide Part 9
sub_heading: "A series of posts to help you become a storage expert!"
redirect_from: /netapp-ground-beginners-guide-part-9/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Beginner's Guide

tags:
- Netapp Newbies
- NetApp
---
## SnapRestore

*   **Reference: [NetApp SnapRestore](http://www.netapp.com/au/products/protection-software/snaprestore.aspx)**

NetApp SnapRestore software uses stored Snapshot copies to recover entire file systems or data volumes in seconds.

 Whether you want to recover a single file or a multi-terabyte data volume, SnapRestore software makes data recovery automatic and almost instantaneous, regardless of your storage capacity or number of files. With a single simple command, you can choose and recover data from any NetApp Snapshot copy on your system.

Whereas traditional data recovery requires that **all** the data be copied from the backup to the source, the SnapRestore process is fast and takes up **very little** of your storage space. With SnapRestore, you can:

*   Restore data files and databases fast
*   Test changes with easy restores to your baseline copy
*   Recover at once from virus attacks, or after user or application error

In addition, SnapRestore software requires no special training, which reduces both the likelihood of operator error and your costs to maintain specialized staffing resources.

## SnapManager

*   **Reference: [Back to Basics: SnapManager Suite](http://community.netapp.com/t5/Tech-OnTap-Articles/Back-to-Basics-SnapManager-Suite/ta-p/86356)**

The more a backup application understands about the way an application works, the more efficient the backup process will be. Unfortunately, back-end storage systems typically know little or nothing about the application data they contain, so you either have to use brute-force methods to perform backups on the storage system or you have to let **each application** perform its own backup. Neither alternative is particularly desirable.

To address this shortcoming, NetApp created its SnapManager software, a **suite** of intelligent tools that allow applications and storage to coordinate activities to make backup fast and space efficient, speed the restore process, and **simplify** common data management tasks. The SnapManager suite represents thousands of man-hours of effort going back to the original SnapManager for Exchange product, which was introduced in 2000.

NetApp users today can choose from seven SnapManager tools that provide **deep integration** to coordinate storage management activities with popular enterprise software programs—Microsoft SQL Server, Exchange, SharePoint, Oracle, and SAP—as well as virtual infrastructure—VMware and Microsoft Hyper-V. These tools offer significant advantages for application backup. They:

*   Integrate closely with the unique features and capabilities of each application.
*   Take full advantage of NetApp data protection features, including Snapshot, SnapMirror, SnapRestore, and FlexClone technologies to provide fast, efficient backup and restore, replication for DR, and cloning. (Cloning is not supported by all SnapManager products.)
*   Allow backups to be completed more quickly in much **less time** (typically minutes versus hours) so backups can be completed more often with **less disruption** to the application.
*   Off-load most of the work of data protection from servers.
*   Provide **application-centric** interfaces that let application administrators perform backups without having to understand details of storage or involve storage administrators in routine activities.
*   Support both Data ONTAP technology operating in 7-Mode and clustered Data ONTAP.

See [**this page**](http://community.netapp.com/t5/Tech-OnTap-Articles/Back-to-Basics-SnapManager-Suite/ta-p/86356) for more information.

## Snap Creator

*   **Reference: [Snap Creator Framework](http://www.netapp.com/au/products/management-software/snapcreator-framework.aspx)**

OS-independent Snap Creator Framework integrates NetApp data protection with a broad range of third-party applications.

NetApp Snap Creator Framework lets you standardize and simplify backup, restore, and DR in any environment. It’s a unified data protection solution for standard and custom applications.

Snap Creator plug-ins integrate NetApp features with third-party applications, operating systems, and databases, including Oracle, VMware, Citrix Xen, Red Hat KVM, DB2, Lotus Domino, MySQL, Sybase ASE, and MaxDB. Snap Creator also accommodates custom plug-ins and has an active developer community.

The Snap Creator Framework provides:

*   **Application-consistent data protection.** You get a centralized solution for backing up critical information, integrating with existing application architectures to assure data consistency and reduce operating costs.
*   **Extensibility.** Achieve fast integration using NetApp modular architecture and policy-based automation.
*   **Cloud readiness.** OS-independent Snap Creator functionality supports physical and virtual platforms and interoperates with IT-as-a-service and cloud environments.

## SnapProtect
### Information

*   **Reference: [Back to Basics: SnapProtect](http://community.netapp.com/t5/Tech-OnTap-Articles/Back-to-Basics-SnapProtect/ta-p/86091)**

An important reason why IT teams choose NetApp storage is because it provides the ability to use integrated data protection technologies like **Snapshot** copies, **SnapMirror** replication, and **SnapVault** disk-to-disk backup. These capabilities dramatically accelerate and simplify backup and replication for DR and other purposes.

Still, we saw a need for **deeper integration** with backup applications, especially for those who need to include tape in their backup environments.

NetApp introduced its SnapProtect management software about a year ago to provide these and other features. NetApp partnered with CommVault to integrate core components of CommVault Simpana with core NetApp technologies. The combination delivers all the benefits you expect from **Snapshot** copies, **SnapMirror**, and **SnapVault**, plus it offers significant advantages including:

*   Accelerates both backup and restore operations
*   Full tape support
*   Cataloging of Snapshot copies, replicas, and tape
*   Built-in support for VMware, Hyper-V, and popular applications
*   Automated provisioning of secondary storage
*   Cascading and fan-out configurations
*   Flexible scheduling and retention
*   Reporting
*   Simple single-pane-of-glass management for all features

### How SnapProtect Is Implemented

*   **Reference: [Back to Basics: SnapProtect](http://community.netapp.com/t5/Tech-OnTap-Articles/Back-to-Basics-SnapProtect/ta-p/86091)**

SnapProtect uses a variety of components. Most of these are familiar NetApp technologies such as:

*   **Snapshot** copies
*   **SnapMirror** replication
*   **SnapVault** for disk-to-disk backup
*   **FlexClone** technology for cloning and indexing
*   **SnapRestore** technology for rapid restore of whole volumes and single files
*   **OnCommand** software (formerly NetApp Operations Manager) for provisioning and replication

In addition, SnapProtect adds several additional components that enable cataloging, coordination, management, and so on.

*   **SnapProtect Server:** Runs Windows, Microsoft SQL Server, and management software
*   **MediaAgents:** Additional servers that help spread the data protection workload during SnapProtect operations
*   **iDataAgents (iDAs):** Software agents installed on backup clients that are responsible for data consistency during backup operations

### SnapProtect Console

*   **Reference: NetApp Training - Fast Track 101: NetApp Portfolio**

SnapProtect provides a single interface (the SnapProtect Console) from which your customers can manage disk-to-tape or disk-to-disk-to-tape backup workflows. They can use the SnapProtect Console to help reduce backup and recovery times, minimize storage and operational costs, and meet requirements for physical and virtualized environments.

In addition to allowing your customers to create and manage Snapshot copies from the single console, the SnapProtect solution lets them manage policies for SnapVault and SnapMirror replication to secondary storage; backup and restore virtual machines, applications, and data; catalog Snapshot copies across both disk and tape for rapid search and retrieval; and work with tape for long-term retention.

The SnapProtect solution is integrated with NetApp storage efficiency, Snapshot, and thin replication technologies. Together, the SnapProtect solution and NetApp technologies reduce backup and recovery windows by up to 98% relative to traditional backup, reduce storage requirements by up to 90%, and reduce network bandwidth utilization. With controller-based licensing, customers can grow their environments and keep software licensing costs down.

### NetApp & CommVault Partnership

*   **Reference: [NetApp to resell CommVault's backup technology](http://www.computerworld.com/article/2508705/data-center/netapp-to-resell-commvault-s-backup-technology.html)**
*   **Reference: [NetApp and CommVault Unite to Modernize Backup](http://www.esg-global.com/blogs/netapp-and-commvault-unite-to-modernize-backup/)**

## Other Posts in this Series

See the [**NetApp From the Ground Up - A Beginner's Guide - Index**](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.