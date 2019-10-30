---
title: Cisco UCS From the Ground Up - A Beginner's Guide Part 1
sub_heading: "A series of posts to help you become a UCS expert!"
redirect_from: /cisco-ucs-from-the-ground-up-a-beginners-guide-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- UCS

tags:
- Cisco
- UCS
---
My **[NetApp From the Ground Up series](/netapp-ground-beginners-guide-index/ "NetApp From the Ground Up – A Beginner’s Guide – Index")** of posts have proven to be quite popular. Though I still have quite a lot of posts left to write in that series, I thought I'd start a UCS series as well.

As with the aforementioned NetApp series, I'm not going to re-invent the wheel and will instead reference the excellent documentation and information which others have already put together.

You may be wondering "if there are already excellent resources out there, why bother blogging about it?". Well, it's for two reasons:

1. I wasn't able to find a structured guide that took readers right from the UCS basics through to more advanced topics, so I thought I'd attempt to fill that gap.
2. Everyone has their own way of explaining things. While I have thoroughly enjoyed reading other peoples material, there are bits and pieces which I feel could be explained a little differently in order to give newcomers a better understanding.

So without further ado, let's get started!

## What is a UCS?

*   **Reference: Me**

It sounds like a simple question, doesn't it? However, if you Google for the answer, it's actually quite difficult to get a clear definition. You may end up finding that most of the results are either full of marketing material, or, are too complex for a newcomer to understand. Take Cisco's [**Overview of Cisco Unified Computing System**](http://www.cisco.com/en/US/docs/unified_computing/ucs/sw/gui/config/guide/141/UCSM_GUI_Configuration_Guide_141_chapter1.pdf) for example. It's a 36 page document, and that's just the overview! To their defence though, they've provided a lot more information than simply explaining what a UCS is.

Unfortunately for newcomers though, you may find a lot of your search results produce similar information. Fortunately Wikipedia has come to the rescue by providing a concise definitions:

### UCS

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

The Cisco Unified Computing System (UCS) is an (x86) architecture data center server platform composed of computing hardware, virtualization support, switching fabric, and management software introduced in 2009. The idea behind the system is to reduce total cost of ownership and improve scalability by integrating the different components into a cohesive platform that can be managed as a single unit. Just-In-Time deployment of resources and 1:N redundancy can be configured with UCS systems.

### Computing

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

The computing component of the UCS is available in two versions: the B-Series (a powered chassis and full and/or half slot blade servers), and the C-series for 19-inch racks (that can be used with fabric interconnects). The compute hardware managed by the UCS Manager software on the Fabric Interconnects can be B-Series (blades), C-Series (rackmount) or a combination of the two. Both form factors use standard components, including Intel processors and DIMM memory. The servers are marketed with converged network adapter and port virtualization. Around 2010, an extended memory technology expanded the number of memory sockets than can be connected to a single memory channel in some models.

### Virtualization

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

Cisco UCS supports several hypervisors including VMware ESX, ESXi, Microsoft Hyper-V, Citrix XenServer and others. VMware Virtualization is provided through a partnership with VMware and uses a version of that company's ESXi. Unlike the VMware Workstation software, ESX and ESXi run directly on the system hardware without the need for any other software (called Bare Metal), and provide the necessary hypervisor functions to host several guest operating systems (such as Windows or Linux) on the physical server. Guest operating systems are limited to 255 GB of vRAM and 8 virtual processors in vSphere 4.x, upgraded to 1 TB of vRAM and 32 vCPUs in vSphere 5.0. Additionally, the Cisco UCS Virtual Interface Cards incorporate VM-FEX technology that gives virtual machines direct access to the hardware for improved performance and network visibility.

### Networking

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

The Cisco 6100 or 6200 Series switch (called a "Fabric Interconnect") provides network connectivity for the chassis, blade servers and rack servers connected to it through 10 Gigabit and Fiber Channel over Ethernet (FCoE). The Fabric Interconnects are derived from the Nexus 5000 and run NX-OS as well as the UCS Manager software. The FCoE component is necessary for connection to SAN storage, since the UCS system blade servers have very little local storage capacity. Cisco currently produces the following series.

The Fabric Interconnect can further connect to multiple Fabric Extenders, Port Extenders using VNTag to the Fabric Interconnects, allowing up to 160 servers to be managed by one Fabric Interconnect (or two in Active-Active failover).

### Management

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

Management of the system devices is handled by the Cisco UCS Manager software embedded into the 6100/6200 series Fabric Interconnect, which is accessed by the administrator through a common browser such as Internet Explorer or Firefox, or a Command line interface like Windows PowerShell or programmatically through an API. Virtual machines can be moved from one physical chassis to another, applications may be moved between virtual machines, and management may even be conducted remotely from an iPhone using SiMU - Simple iPhone Management of UCS. In addition to the embedded software, administrators may also manage the system from VMware's vSphere. The Cisco Integrated Management Controller (CIMC) is also used to configure and manage C-Series stand-alone servers. The CIMC can also be used to manage B-Series blades in addition to the UCS Manager application if configured.

In November 2012, Cisco announced the addition of UCS Central which extends management across multiple domains of UCS and as many as 10,000 servers. UCS Central extends the same model based, open API approach established with UCS Manager.

### Stateless Computing

*   [**Reference: Wikipedia - Cisco Unified Computing System**](http://en.wikipedia.org/wiki/Cisco_Unified_Computing_System)

A key benefit is the concept of Stateless Computing. Each compute node has no set configuration. MAC addresses, UUIDs, firmware and BIOS settings for example, are all configured on the UCS manager in a Service Profile and applied to the servers. This allows for consistent configuration and ease of re-purposing. A new profile can be applied within a matter of minutes.

### Summary

*   **Reference: Me**

I can appreciate that the above may be an information overload for a newcomer, so let me summarise it:

*   **Servers:**
    *   There are two types UCS servers:
        *   **C Series** - "Pizza box" servers which look like your traditional rack mounted server.
        *   **B Series** - Blade servers which slide into the slots of a UCS chassis.
            *   There are two types of B Series servers - half width and full width.
*   **Management:**
    *   Both server series connect to Fabric Interconnects (FIs). The FIs have the UCS Manager software installed on them. This is the software which is used to administer the servers.
    *   **Note:** An exception to this is when a C-Series server operates in "Standalone" mode.
*   **Fabric Interconnects (FIs):**
    *   As well as providing the management interface for your servers, FIs also provide network and storage connectivity for both server series.
    *   **Note:** An exception to this is when a C-Series server operates in "Standalone" mode.
*   **Operating System:**
    *   You're free to install any supported operating system of your choosing. For example, you could install Windows Server 2012 directly on a server, or, you could install ESXi on the server and run Windows 2012 as a VM.
*   **Stateless Computing:**
    *   When a traditional server fails, you may be required to replace it with **identical** hardware otherwise the OS and/or applications may not work correctly. Further to this, you may also be required to spoof unique identifiers such as the original server's MAC address. With UCS servers, the server's entire identity is stored in a profile which can be transferred to any other server. This means that if a server fails, replacing it is as easy as applying the profile to a new server.

## Other Bits and Pieces

*   **Reference: Me**

Two other components which I feel are worth a mention at this stage are:

*   **Vitrual Interface Cards (VICs)**
    *   PCI adapter cards which are used by both B- and C-Series servers.
    *   VICs are used to present virtual adapters (vNICs & vHBAs) to the Operating System(s) installed on the UCS servers.
    *   In regards to B-Series servers, VICs also provide the connectivity between the server and the UCS Chassis's FEXs/IOMs.
*   **Fabric Extenders (FEXs), also known as Input/Output Modules (IOMs) - B-Series Only**
    *   _Note: From here on out I'll use the term IOM instead of FEX. The reason being that using the term FEX may cause confusion as the Nexus 2000 Fabric Extenders are also known as FEXs._
    *   IOMs are inserted into a UCS Chassis and are used to provide the physical connectivity between B-Series servers and the Fabric Interconnects (FIs).
    *   The IOMs  are required because B-Series servers do not have any external ports. Instead, their VIC card(s) connect internally to the IOMs which in turn connect to the FIs. The FIs then provide connectivity to the outside world. By "outside world" I mean systems which reside outside of the UCS platform.

## Diagram

As they say, a picture is worth a thousand words. The diagram below (from [**this**](http://www.definethecloud.net/hp-flex-10-cisco-vic-and-nexus-1000v/) Define the Cloud post) illustrates the information contained in this post.

[![dtc](/assets/2015/02/dtc.png)](/assets/2015/02/dtc.png)

##  Summary

*   **Reference: Me**

To quickly summarise from the top of the diagram down:

1.  The FIs are at the top of the "tree". They provide connectivity to the outside world.
2.  The IOMs provide the physical connectivity between the FIs and the VICs.
3.  vNICs and vHBAs are created ontop of the VICs.
4.  The (virtual) vNICs and vHBAs are presented to the Operating System as physical interfaces.

## Final Thoughts

If you're feeling a bit confused, have another read of this page. As mentioned in [**Part 2 of this series**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-2/), the way in which UCS operates is completely different to the way its predecessors operated, so it may take a little time for you to completely understand it. Never fear though, this series of posts will leave you feeling like a UCS expert :)

## Other Posts in this Series

See the [**Cisco UCS From the Ground Up – A Beginner’s Guide – Index**](/cisco-ucs-from-the-ground-up-index/ "Cisco UCS From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.