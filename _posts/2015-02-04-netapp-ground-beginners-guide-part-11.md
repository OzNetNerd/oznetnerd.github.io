---
title: NetApp From the Ground Up - A Beginner's Guide Part 11
sub_heading: "A series of posts to help you become a storage expert!"
redirect_from: /netapp-ground-beginners-guide-part-11/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Beginner's Guide

tags:
- Netapp Newbies
- NetApp
---

## Capacity
### Right-Sizing

*   **Reference: [NetApp Forum](http://community.netapp.com/t5/FAS-and-V-Series-Storage-Systems-Discussions/Defeating-NetApp-quot-Right-Sizing-quot-for-more-Usable-Capacity/td-p/77165)**

Disk drives from different manufacturers may differ slightly in size even though they belong to the same size category. Right sizing ensures that disks are compatible regardless of manufacturer. Data ONTAP right sizes disks to compensate for different manufacturers producing different raw-sized disks.

### More Information

*   **Reference: [Storage Gaga](https://storagegaga.wordpress.com/2011/10/07/playing-with-netapp-capacity-br/)**

Much has been said about usable disk storage capacity and unfortunately, many of us take the marketing capacity number given by the manufacturer in verbatim. For example, 1TB does not really equate to 1TB in usable terms and that is something you engineers out there should be informing to the customers.

NetApp, ever since the beginning, has been subjected to the scrutiny of the customers and competitors alike about their usable capacity and I intend to correct this misconception. And the key of this misconception is to understand what is the capacity before rightsizing (BR) and after rightsizing (AR).

(Note: Rightsizing in the NetApp world is well documented and widely accepted with different views. It is part of how WAFL uses the disks but one has to be aware that not many other storage vendors publish their rightsizing process, if any)

#### Before Rightsizing (BR)

First of all, we have to know that there are 2 systems when it comes to system of unit prefixes. These 2 systems can be easily said as

*   **Base-10** (decimal) – fit for human understanding
*   **Base-2** (binary) – fit for computer understanding

So according the International Systems of Units, the SI prefixes for Base-10 are:

[![table1](/assets/2015/02/table1.png)](/assets/2015/02/table1.png)

In computer context, where the binary, Base-2 system is relevant, that SI prefixes for Base-2 are

[![table2](/assets/2015/02/table2.png)](/assets/2015/02/table2.png)

And we must know that the storage capacity is in Base-2 rather than in Base-10. Computers are not humans.

With that in mind, the **next issue** are the disk manufacturers. We should have an axe to grind with them for **misrepresenting** the actual capacity. When they say their HDD is 1TB, they are using the Base-10 system i.e. 1TB = 1,000,000,000,000 bytes. **THIS IS WRONG!**

Let’s see how that 1TB works out to be in Gigabytes in the Base-2 system:

1,000,000,000/1,073,741,824 = 931.3225746154785 Gigabytes

**Note:** 2^30 = 1,073,741,824

That result of 1TB, when rounded, is only about 931GB! So, the disk manufacturers aren’t exactly giving you what they have advertised.

Thirdly, and also the most important factor in the BR (Before Rightsizing) phase is how WAFL handles the actual capacity before the disk is produced to WAFL/ONTAP operations. Note that this is all done before all the logical structures of aggregates, volumes and LUNs are created.

In this third point, WAFL formats the actual disks (just like NTFS formats new disks) and this reduces the usable capacity even further. As a starting point, WAFL uses **4K** (4,096 bytes) per block.

_**Note:** It appears that the 4K block size is not the issue, it's the checksum that is the problem._

For Fibre Channel disks, WAFL then formats these blocks as **520 bytes** per sector. Therefore, for each block, **8 sectors** (520 x 8 = 4160 bytes) fill 1 x 4K block, leaving a remainder of 64 bytes (4,160 – 4,096 = 64 bytes). This additional 64 bytes per block is used as a checksum and **is not** displayed by WAFL or ONTAP and **not** accounted for in its usable capacity, therefore the capacity seen by users is further reduced.

**512 bytes** per sector are used for formatting SATA/SAS disks and it consumes **9 sectors** (9 x 512 = 4,608 bytes). 8 sectors will be used for WAFL’s 4K per block (4,096/512 = 8 sectors), while the 9th sector (512 bytes) is used **partially** for its **64 bytes** checksum. As with the Fibre Channel disks, the unused 448 bytes (512 – 64 = 448 bytes) in the 9th sector is not displayed and not part of the usable capacity of WAFL and ONTAP.

And WAFL also compensates for the ever-so-slightly irregularities of the hard disk drives even though they are labelled with similar marketing capacities. That is to say that 1TB from Seagate and 1TB from Hitachi will be different in terms actual capacity. In fact, 1TB Seagate HDD with firmware 1.0a (for ease of clarification) and 1TB Seagate HDD with firmware 1.0b (note ‘a’ and ‘b’) could be different in actual capacity even when both are shipped with a 1.0TB marketing capacity label.

So, with all these things in mind, WAFL does what it needs to do – **Right Size** – to ensure that nothing get screwed up when WAFL uses the HDDs in its aggregates and volumes. All for the right reason – **Data Integrity** – but often criticized for their **“wrongdoing”**. Think of WAFL as your vigilante superhero, wanted by the law for doing good for the people.

In the end, what you are likely to get Before Rightsizing (BR) from NetApp for each particular disk capacity would be:

[![table3](/assets/2015/02/table3.png)](/assets/2015/02/table3.png)

* The size of 34.5GB was for the Fibre Channel Zone Checksum mechanism employed prior to ONTAP version 6.5 of 512 bytes per sector. After ONTAP 6.5, block checksum of 520 bytes per sector was employed for greater data integrity protection and resiliency.

From the table, the percentage of “lost” capacity is shown and to the **uninformed**, this could look significant. But since the percentage value is relative to the Manufacturer’s **Marketing** Capacity, this is **highly inaccurate.** Therefore, competitors **should not** use these figures as FUD and NetApp should use these as a way to properly inform their customers.

#### NetApp Figures

*   **Reference: [NetApp](http://community.netapp.com/fukiw75442/attachments/fukiw75442/fas-and-v-series-storage-systems-discussions/3432/1/Right-sized+disks.pdf)**

[![net1](/assets/2015/02/net1.png)](/assets/2015/02/net1.png)


#### RAID & Right Sizing


See **[Part 3](/netapp-ground-beginners-guide-part-3/ "NetApp From the Ground Up – A Beginner’s Guide Part 3")** for information on RAID & Right Sizing.

#### 4K Blocks

*   [**LinkedIn Discussion**](http://www.linkedin.com/groups/What-are-reasons-behind-fixing-3961404.S.255541032)
*   [**Flash DBA**](http://flashdba.com/4k-sector-size/)

## Other Posts in this Series

See the [**NetApp From the Ground Up - A Beginner's Guide - Index**](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.