---
title: Cisco UCS From the Ground Up - A Beginner's Guide Part 2
sub_heading: "A series of posts to help you become a UCS expert!"
redirect_from: /cisco-ucs-from-the-ground-up-a-beginners-guide-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- UCS

tags:
- Cisco
- UCS
---
In my [**previous post**](/cisco-ucs-from-the-ground-up-a-beginners-guide-part-1/ "Cisco UCS From the Ground Up – A Beginner’s Guide Part 1") I covered Wikipedia's and my versions of an overview of the UCS platform. For good measure, here are a couple of additional "Overview" information:

## UCS Overview #2
### UCS Overview Video

*   **Reference: [TechWise: Fundamentals of Cisco's Unified Computing System, podcast 296](https://www.youtube.com/watch?v=tr3MY7VHqJg "https://www.youtube.com/watch?v=tr3MY7VHqJg")**

### How UCS Differs

*   **Reference: [YouTube: Cisco UCS Servers Big Picture Differences](https://www.youtube.com/watch?v=LAiJ3QlPles "https://www.youtube.com/watch?v=LAiJ3QlPles")**
*   Traditional blade servers require their own SAN and network adapters. UCS chassis connect to a Fabric Interconnect for this connectivity. This results in less adapters having to be purchased as well as less cabling which results in cost savings.
*   Each traditional blade servers needs to be configured to work with an overarching management system. UCS servers are managed by the Fabric Interconnect they connect to.
*   Service Profiles are the fundamental mechanism by which the Cisco Unified Computing System models the necessary abstractions of server, storage, and networking.

### Hardware Overview

*   **Reference:** **[storagemojo: A deep dive into Cisco’s UCS](http://storagemojo.com/2010/04/21/a-deep-dive-into-ciscos-ucs/ "http://storagemojo.com/2010/04/21/a-deep-dive-into-ciscos-ucs/")**

UCS is focused on reducing operating expenses not capital expenses as OPEX is the rising cost. It is proprietary software and support services that breed cost.

Customers are caught on a treadmill, where vendors:

*   Simplify by adding layers and offering services to enable the simplification
*   Which results in a complex management stack and high costs
*   Enterprises can’t easily scale because of legacy systems, which drives application costs higher
*   Management complexity is driving vendor revenues

Sounds something like a StorageMojo critique. Bravo!

The UCS solution is to move to a private cloud powered by VCE – VMware, Cisco and EMC – and other vendors as appropriate. An application-centric unified fabric that ties network and compute resource together under centralized control.

The building blocks of the UCS are:

*   **UCS manager** – the device manager
*   **UCS fabric interconnect** – 20 & 40 port FCoE switches
*   **UCS fabric manager –** the management tool for storage networking across all Cisco SAN and unified fabrics
*   **Fabric extenders –** connect the UCS blade chassis to the switch and simplify cabling, management and diagnostics

All these components are designed to work with both physical and virtual resources.

### More Information

*   **Reference: [storagemojo: A deep dive into Cisco’s UCS](http://storagemojo.com/2010/04/21/a-deep-dive-into-ciscos-ucs/ "http://storagemojo.com/2010/04/21/a-deep-dive-into-ciscos-ucs/")**

The basic Cisco components of the UCS are:

*   **UCS manager:** Cisco UCS Manager implements policy-based management of the server and network resources. Network, storage, and server administrators all create service profiles, allowing the manager to configure the servers, adapters, and fabric extenders and appropriate isolation, quality of service (QoS), and uplink connectivity. It also provides APIs for integration with existing data center systems management tools. An XML interface allows the system to be monitored or configured by upper-level systems management tools.
*   **UCS fabric interconnect:** Networking and management for attached blades and chassis with 10 GigE and FCoE. All attached blades are part of a single management domain. Deployed in redundant pairs, the 20-port and the 40-port offer centralized management with Cisco UCS Manager software and virtual machine optimized services with the support for VN-Link.
*   **Cisco Fabric Manager:** manages storage networking across all Cisco SAN and unified fabrics with control of FC and FCoE. Offers unified discovery of all Cisco Data Center 3.0 devices aa well as task automation and reporting. Enables IT to optimize for the quality-of-service (QoS) levels, performance monitoring, federated reporting, troubleshooting tools, discovery and configuration automation.
*   **Fabric extenders:** connect the fabric to the blade server enclosure, with 10 Gigabit Ethernet connections and simplifying diagnostics, cabling, and management. The fabric extender is similar to a distributed line card and also manages the chassis environment (the power supply, fans and blades) so separate chassis management modules are not required. Each UCS chassis can support up to two fabric extenders for redundancy.

### Additional Information

## Fabric Interconnect

In a UCS environment, you need a Fabric Interconnect to administer your UCS servers. The Fabric Interconnect also required to provide the servers with network and storage connectivity.

If you're using a 5108 series chassis and you're using it in a small domain, you may choose to use a Fabric Interconnect Module. Alternatively, you'll need to use a You will have to use a standalone Fabric Interconnect chassis. If you choose the latter option, you will need a Fabric Extender.

### Fabric Extenders

If you were to opt for a Fabric Interconnect chassis (as covered in the “Fabric Interconnect” section above), you'll also need to insert at least one Fabric Extender in your B Series servers.

## Other Posts in this Series

See the [**Cisco UCS From the Ground Up – A Beginner’s Guide – Index**](/cisco-ucs-from-the-ground-up-index/ "Cisco UCS From the Ground Up – A Beginner’s Guide – Index") post for links to all of the posts in this series.