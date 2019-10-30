---
title: NetApp From the Ground Up - A Beginner's Guide Part 4
sub_heading: "A series of posts to help you become a storage expert!"
redirect_from: /netapp-ground-beginners-guide-part-4/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Beginner's Guide

tags:
- Netapp Newbies
- NetApp
---
## WAFL

*   **Reference: [Bitpushr: How Data ONTAP caches, assembles and writes data](http://bitpushr.wordpress.com/2014/07/28/how-data-ontap-caches-assembles-and-writes-data/)**

WAFL is our Write Anywhere File Layout. If NVRAM’s role is the most-commonly misunderstood, WAFL comes in 2nd. Yet WAFL has a simple goal, which is to write data in full stripes across the storage media. WAFL acts as an intermediary of sorts — there is a top half where files and volumes sit, and a bottom half ([**reference**](http://ntptest.typepad.com/dave/2008/12/is-wafl-a-files.html)) that interacts with RAID, manages SnapShots and some other things. WAFL isn’t a filesystem, but it does some things a filesystem does; it can also contain filesystems. WAFL contains mechanisms for dealing with files & directories, for interacting with volumes & aggregates, and for interacting with RAID. If Data ONTAP is the heart of a NetApp controller, WAFL is the blood that it pumps.

Although WAFL can write anywhere we want, in reality we write where it makes the most sense: in the closest place (relative to the disk head) where we can write a complete stripe in order to minimize seek time on subsequent I/O requests. WAFL is optimized for writes, and we’ll see why below. Rather unusually for storage arrays, we can write client data and metadata anywhere.

A colleague has this to say about WAFL, and I couldn’t put it better:

_There is a relatively simple “cheating at Tetris” analogy that can be used to articulate WAFL’s advantages. It is not hard to imagine how good you could be at Tetris if you were able to review the next thousand shapes that were falling into the pattern, rather than just the next shape._

_Now imagine how much better you could be at Tetris if you could take any of the shapes from within the next thousand to place into your pattern, rather than being forced to use just the next shape that is falling._

Finally, imagine having plenty of time to review the next thousand shapes and plan your layout of all 1,000, rather than just a second or two to figure out what to do with the next piece that is falling. In summary, you could become the best Tetris player on Earth, and that is essentially what WAFL is in the arena of data allocation techniques onto underlying disk arrays.

The Tetris analogy incredibly important, as it directly relates to the way that NetApp uses WAFL to optimize for writes. Essentially, we collect random I/O that is destined to be written to disk, reorganize it so that it resembles sequential I/O as much as possible, and then write it to disk sequentially. Another way of explaining this behavior is that of write coalescing: we reduce the number of operations that ultimately land on the disk, because we re-organize them in memory before we commit them to disk and we wait until we have a bunch of them before committing them to disk via a Consistency Point. Put another way, write coalescing allows to avoid the common (and expensive) RAID workflow of “read-modify-write”.

Note: We write the client’s data from RAM (not from NVRAM) to disk. ([**Reference**](http://bitpushr.wordpress.com/2014/07/28/how-data-ontap-caches-assembles-and-writes-data/)).

### More Information

*   **Reference: NetApp University - Introduction to NetApp Products**

The Data ONTAP operating system supports production environments with robust and distinctive data protection technologies that are not found in other storage systems. How can a storage system that uses disk drives just like everyone else provide such unique functionality? The secret to the success of the Data ONTAP operating system is found in its core technologies: Write Anywhere File Layout (WAFL), NVRAM, and **Raid-DP**. These components power the exclusive data management capabilities of the Data ONTAP operating system.

The WAFL file system was developed with three architectural principles: optimize for writes, integrate nonvolatile storage, and support RAID. The WAFL layer optimizes write activity by organizing blocks of incoming data so that they can be written **simultaneously**, across multiple disks, to enable maximum **parallelism**. Internal file system metadata, known as **inodes** or **pointers**, is written alongside production data, minimizing the need for the performance-impacting seek operations common to other file systems.

In NAS environments, users access the WAFL file system directly through shares and exports. In SAN environments, WAFL technology functions as a virtualization layer, which enables the Data ONTAP process of optimizing block layout to remain independent from the host's proprietary layout inside the LUN.

The WAFL virtualization layer does a lot more than just put blocks on a disk. This additional processing could introduce latency, but it does not. NVRAM is the key component for delivering fast, low-latency data access while WAFL technology virtualizes the storage subsystem. Each write or update request that the storage system receives is logged to NVRAM and mirrored to the partner system's NVRAM. Because the data is now protected by battery backup and the partner mirror, the system can send the write acknowledgement without waiting for the storage layer, which is much slower than NVRAM. In this way, data center production proceeds over a purely electronic data path, resulting in high-speed, low-latency write and update activity. The WAFL layer commits the writes to the storage medium, disk or flash, independently. Each block of data must be successfully written before it is cleared from NVRAM. NVRAM secures data and increases performance while the WAFL layer intelligently organizes the destination storage structure.

[![wafl](/assets/2015/02/wafl.png)](/assets/2015/02/wafl.png)

## NVRAM

*   **Reference: [Bit Pushr: How Data ONTAP caches, assembles and writes data](http://bitpushr.wordpress.com/2014/07/28/how-data-ontap-caches-assembles-and-writes-data/)**

Physically, NVRAM is little more than RAM with a battery backup. Our NVRAM contains a **transaction log** of client I/O that has not yet been written to disk from RAM by a consistency point. Its primary mission is to **preserve** that not-yet-written data in the event of a power outage or similar, severe problem. Our controllers vary from **768MB** of NVRAM (FAS2220) to **4GB** of NVRAM (FAS6290). In my opinion, NVRAM’s function is perhaps the most-commonly **misunderstood** part of our architecture. NVRAM is simply a **double-buffered journal** of pending write operations. NVRAM, therefore, is simply a **redo log** — it _**is not**_ the write cache! After data is written to NVRAM, it is not looked at again unless you experience a dirty shutdown. This is because NVRAM’s importance to performance comes from software.

In an HA pair environment where two controllers are connected to each other, NVRAM is mirrored between the two nodes. Its primary mission is to preserve data that not-yet-written data in the event a partner controller suffers a power outage or similar severe problem. NVRAM **mirroring** happens for HA pairs in Data ONTAP 7-mode, HA pairs in clustered Data ONTAP and HA pairs in MetroCluster environments.

## Other Posts in this Series

See the [**NetApp From the Ground Up - A Beginner's Guide - Index**](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.