---
title: NetApp From the Ground Up - A Beginner's Guide Part 13
sub_heading: "A series of posts to help you become a storage expert!"
redirect_from: /netapp-from-the-ground-up-a-beginners-guide-part-13/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Beginner's Guide

tags:
- Netapp Newbies
- NetApp
---
## Snap Reserve
### Overview

*   **Reference: [NetApp - Understanding Snapshot copy reserve](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-901983DC-6BF0-4C84-8B55-257403340C8B.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-901983DC-6BF0-4C84-8B55-257403340C8B.html")**

Snapshot copy reserve sets a specific percent of the disk space for storing Snapshot copies. If the Snapshot copies exceed the reserve space, they **spill** into the active file system and this process is called **snapreserveSnapshot spill**.

The Snapshot copy reserve must have **sufficient** space allocated for the Snapshot copies. If the Snapshot copies exceeds the reserve space, you must **delete** the existing Snapshot copies from the active file system to recover the space, for the use of the file system. You can also modify the percent of disk space that is allotted to Snapshot copies.

### Defaults

*   **Reference: [NetApp - What the Snapshot copy reserve is](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-98CB77E0-4E7E-4495-BF48-B02821FDA3C7.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-98CB77E0-4E7E-4495-BF48-B02821FDA3C7.html")**

The Snapshot copy reserve sets a specific percent of the disk space for Snapshot copies. For traditional volumes, the default Snapshot copy reserve is set to **20 percent** of the disk space. For FlexVol volumes, the default Snapshot copy reserve is set to **5 percent** of the disk space.

The active file system cannot consume the Snapshot copy reserve space, but the Snapshot copy reserve, if exhausted, can use space in the active file system.

#### Diagram

*   **Reference: [NetApp Community: Understanding Aggregate And LUN](http://community.netapp.com/t5/FAS-and-V-Series-Storage-Systems-Discussions/Understanding-aggregate-and-lun/td-p/23326 "http://community.netapp.com/t5/FAS-and-V-Series-Storage-Systems-Discussions/Understanding-aggregate-and-lun/td-p/23326")**
*   **Note: See [Part 3](/netapp-ground-beginners-guide-part-3/ "NetApp From the Ground Up – A Beginner’s Guide Part 3") for more information on the diagram below**

[![snapshot](/assets/2015/02/snapshot.jpg)](/assets/2015/02/snapshot.jpg)

## Can NetApp Snapshots be used as Backups?
### Information

*   **Reference: [Server Fault - Can NetApp Snapshots be used as Backups?](http://serverfault.com/questions/588670/can-netapp-snapshots-be-used-as-backups "http://serverfault.com/questions/588670/can-netapp-snapshots-be-used-as-backups")**

Backups serve two functions.

*   First and foremost, they're there to allow you to recover your data if it becomes unavailable. In this sense, snapshots are not backups. If you lose data on the filer (volume deletion, storage corruption, firmware error, etc.), all snapshots for that data are gone as well.
*   Secondly, and far more commonly, backups are used to correct for routine things like accidental deletions. In this use case, snapshots are backups. They're arguably one of the best ways to provide this kind of recovery, because they make the earlier versions of the data available directly to the users or their OS as a .snapshot hidden directory that they can directly read their file from.

### No retention policy

That said, while we have snapshots and use them extensively, we still do nightly incrementals on Netbackup to tape or data domain. The reason is that snapshots can not reliably uphold a retention policy. If you tell users that they will be able to back up from a daily granularity for a week then a weekly granularity for a month, you can't keep that promise with snapshots.

On a Netapp volume with snapshots, deleted data contained in a snapshot occupies “snap reserve” space. If the volume isn't full and you've configured it this way, you can also push past that snapshot reserve and have snapshots that occupy some of the unused data space. If the volume fills up, though, all the snapshots but the ones supported by data in the reserved space will get deleted. Deletion of snapshots is determined only by available snapshot space, and if it needs to delete snapshots that are required for your retention policy, it will.

Consider this situation:

*   A full volume with regular snapshots and a 2 week retention requirement.
*   Assume half of the reserve in use for snapshots based on the normal rate of change.
*   Someone deletes a lot of data (more than the snapshot reserve), drastically increasing the rate of change, temporarily.
*   At this point, your snapshot reserve is completely used, as is as much of the data free space you've allowed OnTap to use for snapshots, but you haven't lost any snapshots yet. As soon as someone fills the volume back up with data, though, you'll lose all the snapshots contained in the data section, which will push your recovery point back to the time just after the large deletion.

### Summary

Netapp snapshots don't cover you against real data loss. An errant deleted volume or data loss on the filer will require you to rebuild data.

They are a very simple and elegant way to allow for simple routine restores, but they aren't reliable enough that they replace a real backup solution. Most of the time, they'll make routine restores simple and painless, but when they're not available, you are exposed.

## Volume Snap Reserve

*   **Reference: [NetApp: What the Snapshot copy reserve is](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-98CB77E0-4E7E-4495-BF48-B02821FDA3C7.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-98CB77E0-4E7E-4495-BF48-B02821FDA3C7.html")**

The Snapshot copy reserve sets a specific percent of the disk space for Snapshot copies. For **traditional** volumes, the **default** Snapshot copy reserve is set to **20 percent** of the disk space. For **FlexVol** volumes, the **default** Snapshot copy reserve is set to **5 percent** of the disk space.

The active file system **cannot** consume the Snapshot copy reserve space, but the Snapshot copy reserve, if exhausted, **can** use space in the active file system. _(See **"Snapshot Spill"** below for more information)._

## Aggregate Snap Reserve
### Information

*   **Reference: [NetApp - Managing aggregate Snapshot copies](https://library.netapp.com/ecmdocs/ECMP1155684/html/GUID-2372623A-FC98-42FF-8C26-B7482E7D5A7C.html "https://library.netapp.com/ecmdocs/ECMP1155684/html/GUID-2372623A-FC98-42FF-8C26-B7482E7D5A7C.html")**

An aggregate Snapshot copy is a **point-in-time, read-only** image of an **aggregate**. You use aggregate Snapshot copies when the contents of an **entire aggregate** need to be recorded.

An aggregate Snapshot copy is similar to a **volume** Snapshot copy, except that it captures the contents of the **entire** aggregate, rather than any particular volume. Also, you **do not** restore data directly from an aggregate Snapshot copy. To restore data, you use a **volume** Snapshot copy.

How you use aggregate Snapshot copies depends on whether you use the **SyncMirror** or **MetroCluster** functionality.

*   If you use **SyncMirror** or **MetroCluster**, you must enable **automatic** aggregate Snapshot copy creation and keep your aggregate Snapshot reserve at **5 percent** or higher.
    *   If you use **SyncMirror** or **MetroCluster** and you need to **break** the mirror, an aggregate Snapshot copy is created **automatically** before breaking the mirror to decrease the time it takes to resynchronize the mirror later.
    *   Also, if you are making a global change to your storage system and you want to be able to restore the entire system state if the change produces unexpected results, you take an aggregate Snapshot copy before making the change.
*   If you **do not** use either **SyncMirror** or **MetroCluster**, you **do not** need to enable automatic aggregate Snapshot copy creation or reserve space for aggregate Snapshot copies.
    *   If the aggregate file system becomes **inconsistent**, aggregate Snapshot copies can be used by technical support to restore the file system to a consistent state. If that is important to you, you can ensure that **automatic** aggregate Snapshot copy creation is enabled. However, disabling automatic aggregate Snapshot copy creation and keeping your aggregate Snapshot reserve at **0 percent** increases your storage utilization, because no disk space is reserved for aggregate Snapshot copies. **Disabling** automatic aggregate Snapshot copy creation and setting the aggregate Snapshot reserve to 0 percent **does not** affect normal operation, except for making more free space available for data.

**Note:** The default size of the aggregate Snapshot reserve is 5 percent of the aggregate size. For example, if the size of your aggregate is 500 GB, then 25 GB is set aside for aggregate Snapshot copies.

**Note:** Unlike volume Snapshot copies, aggregate Snapshot copies **cannot** consume any space outside of their Snapshot reserve, **if** automatic aggregate Snapshot copy deletion is enabled. If automatic aggregate Snapshot copy deletion is disabled, then aggregate Snapshot copies **can** consume space outside of their Snapshot reserve.

### More Information

*   **Reference: Me**

_As per **[this page](http://netapp-blog.blogspot.com.au/2010/12/how-to-restore-data-from-aggregate_02.html "http://netapp-blog.blogspot.com.au/2010/12/how-to-restore-data-from-aggregate_02.html")**, **Aggregate Snapshots** aren't used very often. As seen in the comments, people prefer using **Volume Snapshots** instead._

## Snapshot Spill
### Information

*   **Reference: [NetApp - How Snapshot copies and Snapshot reserve use space in a volume](https://library.netapp.com/ecmdocs/ECMP1196906/html/GUID-38F4D6E3-394F-43DB-8A99-6581DC568B17.html "https://library.netapp.com/ecmdocs/ECMP1196906/html/GUID-38F4D6E3-394F-43DB-8A99-6581DC568B17.html")**

Understanding the Snapshot reserve area of a FlexVol volume or Infinite Volume and what Snapshot spill is can help you correctly size the Snapshot reserve. For FlexVol volumes, it can help you decide whether to enable the Snapshot autodelete capability.

When Snapshot copies use more space than the Snapshot reserve, they spill over and use space in the active file system. The Snapshot reserve area of a volume is the space reserved exclusively for Snapshot copies. It is not available to the user data or metadata area of the volume. The size of the Snapshot reserve is a specified percentage of the current volume size, and does not depend on the number of Snapshot copies or how much space they consume.

If all of the space allotted for the Snapshot reserve is used but the active file system (user data and metadata) is not full, Snapshot copies can use more space than the Snapshot reserve and spill into the active file system. This extra space is called Snapshot spill.

The following illustration shows a FlexVol volume with no Snapshot spill occurring. The two blocks on the left show the volume's used and available space for user data and metadata. The two blocks on the right show the used and unused portions of the Snapshot reserve. When you modify the size of the Snapshot reserve, it is the blocks on the right that change.

[![ss1](/assets/2015/02/ss1.gif)](/assets/2015/02/ss1.gif)

The following illustration shows a FlexVol volume with Snapshot spill occurring. The Snapshot reserve area is full and Snapshot copies spilling over into a Spill area that is part of the user data and metadata area's available space. The size of the Snapshot reserve remains the same.

[![ss2](/assets/2015/02/ss2.gif)](/assets/2015/02/ss2.gif)

## Why Managing Snapshot Reserve is Important
### Information

*   **Reference: [Why Managing Snapshot Reserve is Important](http://www.cosonok.com/2013/12/why-managing-snapshot-reserve-is.html "http://www.cosonok.com/2013/12/why-managing-snapshot-reserve-is.html")**

This post is essentially a continuation of Understanding NetApp Snapshots (was thinking to call it part II except this subject deserves its own title!) Here we’ll see what happens when snapshots exceed their snapshot reserve, and what can potentially happen if snapshots are allowed to build up too much.

At the end of the aforementioned post, we had our 1024MB volume with 0MB file-system space used, and 20MB of snapshot reserve used. The volume has a 5% snapshot reserve (for completeness: it is also thin-provisioned/space-guarantee=none, and has a 0% fractional reserve.)

```
Filesystem               total   used   avail capacity
/vol/cdotshare/          972MB    0MB   972MB       0%
/vol/cdotshare/.snapshot  51MB   20MB    30MB      40%
``` 

Our client sees an empty volume (share) of size 972MB (95% of 1024MB).

**Image: Empty Share**

[![sc1](/assets/2015/02/sc1.png)](/assets/2015/02/sc1.png)

What does the client see if we increase the snapshot reserve to 20%?

```
::> volume modify cdotshare -percent-snapshot-space 20
``` 

**Image: Empty Share but less user visible space due to increased snapshot reserve**

[![sc2](/assets/2015/02/sc2.png)](/assets/2015/02/sc2.png)

Our client sees the volume size has reduced to 819MB (80% of 1024MB).

So, we now have 204MB of snapshot reserve with 20MB of that reserve used.

```
::> df -megabyte cdotshare
Filesystem               total       used      avail capacity
/vol/cdotshare/          819MB        0MB      819MB       0%
/vol/cdotshare/.snapshot 204MB       21MB      183MB      10%
``` 

What happens if we overfill the snapshot reserve to say 200% (408MB) by adding data, creating snapshot, and then deleing all the data? What does the client see?

1. Add ~380MB to the share via the client
2. Take a snapshot

    ```
    ::> snapshot create cdotshare -snapshot snap04 -vserver vs1
    ```

3.  Using the client, delete everything in the share so the additional 380MB is also locked in snapshots

    So, our snapshot capacity is now at 200% (that’s twice its reserve) and the “df” output shows there’s only 613MB available user capacity.

    ```
    Filesystem               total       used      avail capacity
    /vol/cdotshare/          819MB      205MB      613MB      25%
    /vol/cdotshare/.snapshot 204MB      410MB        0MB     200%
    ```

    The 613MB available user capacity is confirmed via the client, even though volume is actually empty.

    **Image: Share now occupied but not by active file-system data**

    [![sc3](/assets/2015/02/sc3.png)](/assets/2015/02/sc3.png)

    **Image: The share says it is empty!**

    [![sc4](/assets/2015/02/sc4.png)](/assets/2015/02/sc4.png)

    If you ever get questions like “my volume’s empty but something’s using up my space - what is it?” now you know what the answer might be - snapshots going over their reserve!

    ```
    ::> snapshot show cdotshare
    Vserver  Volume  Snapshot    State        Size Total% Used%
    -------- ------- ----------- -------- -------- ------ -----
    vs1      cdotshare
                     snap01      valid        92KB     0%   15%
                     snap02      valid     20.61MB     2%   98%
                     snap03      valid     184.5MB    18%  100%
                     snap04      valid       205MB    20%  100%<
    ```

    To take it to the furthest, I could completely fill up the volume with snapshots (by adding data, taking a snapshot, deleting data) and then there’s no space for any active file-system - that is - the user will see an empty but completely full volume! Or - better put - a volume empty of active user data but otherwise full due to snapshot retention of past changes/deletions.

    **Image: Empty but completely full volume!**

    [![sc5](/assets/2015/02/sc5.png)](/assets/2015/02/sc5.png)

    ```
    Filesystem               total       used      avail capacity
    /vol/cdotshare/          819MB      818MB        0MB     100%
    /vol/cdotshare/.snapshot 204MB     1022MB        0MB     499%
    ```

### What are (some) ways to manage snapshots?

1. The volume should be sized correctly for the amount of data that’s going to go in it (keeping in mind the need for growth), and the snapshot reserve should be of a size that will contain the changes (deletions/block modifications) over the retention period required.
2.  The snapshot policy should be set appropriately:

    ```
    ::> volume modify -vserver VSERVER -volume VOLUME -snapshot-policy POLICY
    ```

    **Note:** Changing the snapshot policy will require manual deletion of snapshots that were controlled by the previous policy.

3. Consider making using of these space and snapshot management features:

    ```
    ::> volume modify -vserver VSERVER -volume VOLUME -?
    -autosize {true|false}
    -max-autosize {integer(KB/MB/GB/TB/PB)}
    -autosize-increment {integer(KB/MB/GB/TB/PB)}
    -space-mgmt-try-first {volume_grow/snap_delete}
    ```

## Monitoring, Events and Alerting

OnCommand Unified Manager (OCUM) is available and free to use - OCUM can monitor your snapshot reserve utilization levels and much more!

## Finally - Fractional Reserve

Going back to our example, what would it look like if we set the fractional reserve to 100%?

The answer is no different:

```
::> volume modify -vserver vs1 -volume cdotshare -fractional-reserve 100%

::> df -megabyte cdotshare
Filesystem               total       used      avail capacity
/vol/cdotshare/          819MB      818MB        0MB     100%
/vol/cdotshare/.snapshot 204MB     1022MB        0MB     499%
``` 

The volume is still full of snapshot with no space for active filesystem data!

### More Snapshot Information

See the following pages for more information:

*   **[https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-132FA703-6109-4BAB-9C04-D375E1DB0184.html](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-132FA703-6109-4BAB-9C04-D375E1DB0184.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-132FA703-6109-4BAB-9C04-D375E1DB0184.html")**
*   **[https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-4547DD0A-4A55-4982-89A0-90AD8A1C86F4.html](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-4547DD0A-4A55-4982-89A0-90AD8A1C86F4.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-4547DD0A-4A55-4982-89A0-90AD8A1C86F4.html")**
*   **[https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-7FD0912C-6C8C-420E-B1FD-B05A3E3D3180.html](https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-7FD0912C-6C8C-420E-B1FD-B05A3E3D3180.html "https://library.netapp.com/ecmdocs/ECMP1196991/html/GUID-7FD0912C-6C8C-420E-B1FD-B05A3E3D3180.html")**
*   **[http://broadcast.oreilly.com/2009/02/understanding-snapshot-consump.html](http://broadcast.oreilly.com/2009/02/understanding-snapshot-consump.html "http://broadcast.oreilly.com/2009/02/understanding-snapshot-consump.html")**

## Other Posts in this Series

See the [**NetApp From the Ground Up - A Beginner's Guide - Index**](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.