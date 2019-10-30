---
title: Interacting with NetApp APIs, Part 3
sub_heading: "A series of posts to help you get started with NetApp APIs"
redirect_from: /interacting-netapp-apis-part-3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Labs
- NetApp
- Beginner's Guide
- Automation
- Python

tags:
- Netapp Newbies
- NetApp
- Automation
- Python
- DevOps
---
In [**Part 2 of this series**](/interacting-netapp-apis-part-2/) we made our first API call and received over 200 lines of XML as a result. The reason why we received so much output is because we didn't remove any desired-attributes  and therefore the call retrieved about 90 pieces of information. When you multiply that by the number of queries we ran (2), you get 180 pieces of information being requested.

This post will discuss why you should limit your queries to only the pieces of information you're interested in. It will also cover how you can use ZExplore to convert its XML configuration to languages such as Python, Perl and Ruby.

## Converting Code

As discussed previously in this series, ZExplore outputs your queries into the **"Execute"** tab in XML. However, you can also have it convert the queries to any one of the following languages:

*   C#
*   C
*   Java
*   Perl
*   Python
*   Ruby

You do this by navigating to **"Preferences -> Languages"**. Once you've selected a language the converted code is available in the **"Develop"** tab.

This is a fantastic feature because it allows you to add your NetApp API calls to your scripts simply by copying and pasting the generated code. Using this feature I'm able to combine my NetApp API calls with my Cisco MDS code which will enable me to automate the provisioning of storage for new hosts connected to the SAN network.

## Cleaner Code

Cleaner code means you and anyone else who works with your code, is able to do their job more efficiently.  For example, say I'm only interested in retrieving:

*   A list of nodes who own aggregates with the name **"aggr0*"**.

Instead of generating over **140** lines of XML code:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<netapp  xmlns="http://www.netapp.com/filer/admin" version="1.32">
  <aggr-get-iter>
    <desired-attributes>
      <aggr-attributes>
        <aggr-64bit-upgrade-attributes>
          <aggr-check-attributes>
            <added-space></added-space>
            <check-last-errno></check-last-errno>
            <cookie></cookie>
            <is-space-estimate-complete></is-space-estimate-complete>
          </aggr-check-attributes>
          <aggr-start-attributes>
            <min-space-for-upgrade></min-space-for-upgrade>
            <start-last-errno></start-last-errno>
          </aggr-start-attributes>
          <aggr-status-attributes>
            <is-64-bit-upgrade-in-progress></is-64-bit-upgrade-in-progress>
          </aggr-status-attributes>
        </aggr-64bit-upgrade-attributes>
        <aggr-fs-attributes>
          <block-type></block-type>
          <fsid></fsid>
          <type></type>
        </aggr-fs-attributes>
        <aggr-inode-attributes>
          <files-private-used></files-private-used>
          <files-total></files-total>
          <files-used></files-used>
          <inodefile-private-capacity></inodefile-private-capacity>
          <inodefile-public-capacity></inodefile-public-capacity>
          <maxfiles-available></maxfiles-available>
          <maxfiles-possible></maxfiles-possible>
          <maxfiles-used></maxfiles-used>
          <percent-inode-used-capacity></percent-inode-used-capacity>
        </aggr-inode-attributes>
        <aggr-ownership-attributes>
          <home-id></home-id>
          <home-name></home-name>
          <owner-id></owner-id>
          <owner-name></owner-name>
        </aggr-ownership-attributes>
        <aggr-performance-attributes>
          <free-space-realloc></free-space-realloc>
        </aggr-performance-attributes>
        <aggr-raid-attributes>
          <checksum-status></checksum-status>
          <checksum-style></checksum-style>
          <disk-count></disk-count>
          <ha-policy></ha-policy>
          <has-local-root></has-local-root>
          <has-partner-root></has-partner-root>
          <is-checksum-enabled></is-checksum-enabled>
          <is-hybrid></is-hybrid>
          <is-hybrid-enabled></is-hybrid-enabled>
          <is-inconsistent></is-inconsistent>
          <mirror-status></mirror-status>
          <mount-state></mount-state>
          <plex-count></plex-count>
          <plexes>
            <plex-attributes>
              <is-online></is-online>
              <is-resyncing></is-resyncing>
              <plex-name></plex-name>
              <raidgroups>
                <raidgroup-attributes>
                  <checksum-style></checksum-style>
                  <is-recomputing-parity></is-recomputing-parity>
                  <is-reconstructing></is-reconstructing>
                  <raidgroup-name></raidgroup-name>
                  <recomputing-parity-percentage></recomputing-parity-percentage>
                  <reconstruction-percentage></reconstruction-percentage>
                </raidgroup-attributes>
              </raidgroups>
              <resyncing-percentage></resyncing-percentage>
            </plex-attributes>
          </plexes>
          <raid-lost-write-state></raid-lost-write-state>
          <raid-size></raid-size>
          <raid-status></raid-status>
          <state></state>
        </aggr-raid-attributes>
        <aggr-snaplock-attributes>
          <is-snaplock></is-snaplock>
          <snaplock-type></snaplock-type>
        </aggr-snaplock-attributes>
        <aggr-snapmirror-attributes>
          <dp-snapmirror-destinations></dp-snapmirror-destinations>
          <ls-snapmirror-destinations></ls-snapmirror-destinations>
          <mv-snapmirror-destinations></mv-snapmirror-destinations>
        </aggr-snapmirror-attributes>
        <aggr-snapshot-attributes>
          <files-total></files-total>
          <files-used></files-used>
          <maxfiles-available></maxfiles-available>
          <maxfiles-possible></maxfiles-possible>
          <maxfiles-used></maxfiles-used>
          <percent-inode-used-capacity></percent-inode-used-capacity>
          <percent-used-capacity></percent-used-capacity>
          <size-available></size-available>
          <size-total></size-total>
          <size-used></size-used>
        </aggr-snapshot-attributes>
        <aggr-space-attributes>
          <hybrid-cache-size-total></hybrid-cache-size-total>
          <percent-used-capacity></percent-used-capacity>
          <size-available></size-available>
          <size-total></size-total>
          <size-used></size-used>
          <total-reserved-space></total-reserved-space>
        </aggr-space-attributes>
        <aggr-striping-attributes>
          <member-count></member-count>
        </aggr-striping-attributes>
        <aggr-volume-count-attributes>
          <flexvol-count></flexvol-count>
          <flexvol-count-collective></flexvol-count-collective>
          <flexvol-count-not-online></flexvol-count-not-online>
          <flexvol-count-quiesced></flexvol-count-quiesced>
          <flexvol-count-striped></flexvol-count-striped>
        </aggr-volume-count-attributes>
        <aggr-wafliron-attributes>
          <last-start-errno></last-start-errno>
          <last-start-error-info></last-start-error-info>
          <scan-percentage></scan-percentage>
          <state></state>
        </aggr-wafliron-attributes>
        <aggregate-name></aggregate-name>
        <aggregate-uuid></aggregate-uuid>
        <nodes>
          <node-name></node-name>
        </nodes>
        <striping-type></striping-type>
      </aggr-attributes>
    </desired-attributes>
    <max-records>2</max-records>
    <query>
      <aggr-attributes>
        <aggregate-name>aggr0*</aggregate-name>
      </aggr-attributes>
    </query>
    <tag></tag>
  </aggr-get-iter>
</netapp>
```

and over **200** lines of Python code:

```python
import sys
sys.path.append("/lib/python/NetApp")
from NaServer import *

s = NaServer("192.168.0.205", 1 , 32)
s.set_server_type("FILER")
s.set_transport_type("HTTPS")
s.set_port(443)
s.set_style("LOGIN")
s.set_admin_user("admin", "")

api = NaElement("aggr-get-iter")

xi = NaElement("desired-attributes")
api.child_add(xi)

xi1 = NaElement("aggr-attributes")
xi.child_add(xi1)

xi2 = NaElement("aggr-64bit-upgrade-attributes")
xi1.child_add(xi2)

xi3 = NaElement("aggr-check-attributes")
xi2.child_add(xi3)

xi3.child_add_string("added-space","")
xi3.child_add_string("check-last-errno","")
xi3.child_add_string("cookie","")
xi3.child_add_string("is-space-estimate-complete","")

xi4 = NaElement("aggr-start-attributes")
xi2.child_add(xi4)

xi4.child_add_string("min-space-for-upgrade","")
xi4.child_add_string("start-last-errno","")

xi5 = NaElement("aggr-status-attributes")
xi2.child_add(xi5)

xi5.child_add_string("is-64-bit-upgrade-in-progress","")

xi6 = NaElement("aggr-fs-attributes")
xi1.child_add(xi6)

xi6.child_add_string("block-type","")
xi6.child_add_string("fsid","")
xi6.child_add_string("type","")

xi7 = NaElement("aggr-inode-attributes")
xi1.child_add(xi7)

xi7.child_add_string("files-private-used","")
xi7.child_add_string("files-total","")
xi7.child_add_string("files-used","")
xi7.child_add_string("inodefile-private-capacity","")
xi7.child_add_string("inodefile-public-capacity","")
xi7.child_add_string("maxfiles-available","")
xi7.child_add_string("maxfiles-possible","")
xi7.child_add_string("maxfiles-used","")
xi7.child_add_string("percent-inode-used-capacity","")

xi8 = NaElement("aggr-ownership-attributes")
xi1.child_add(xi8)

xi8.child_add_string("home-id","")
xi8.child_add_string("home-name","")
xi8.child_add_string("owner-id","")
xi8.child_add_string("owner-name","")

xi9 = NaElement("aggr-performance-attributes")
xi1.child_add(xi9)

xi9.child_add_string("free-space-realloc","")

xi10 = NaElement("aggr-raid-attributes")
xi1.child_add(xi10)

xi10.child_add_string("checksum-status","")
xi10.child_add_string("checksum-style","")
xi10.child_add_string("disk-count","")
xi10.child_add_string("ha-policy","")
xi10.child_add_string("has-local-root","")
xi10.child_add_string("has-partner-root","")
xi10.child_add_string("is-checksum-enabled","")
xi10.child_add_string("is-hybrid","")
xi10.child_add_string("is-hybrid-enabled","")
xi10.child_add_string("is-inconsistent","")
xi10.child_add_string("mirror-status","")
xi10.child_add_string("mount-state","")
xi10.child_add_string("plex-count","")

xi11 = NaElement("plexes")
xi10.child_add(xi11)

xi12 = NaElement("plex-attributes")
xi11.child_add(xi12)

xi12.child_add_string("is-online","")
xi12.child_add_string("is-resyncing","")
xi12.child_add_string("plex-name","")

xi13 = NaElement("raidgroups")
xi12.child_add(xi13)

xi14 = NaElement("raidgroup-attributes")
xi13.child_add(xi14)

xi14.child_add_string("checksum-style","")
xi14.child_add_string("is-recomputing-parity","")
xi14.child_add_string("is-reconstructing","")
xi14.child_add_string("raidgroup-name","")
xi14.child_add_string("recomputing-parity-percentage","")
xi14.child_add_string("reconstruction-percentage","")
xi12.child_add_string("resyncing-percentage","")
xi10.child_add_string("raid-lost-write-state","")
xi10.child_add_string("raid-size","")
xi10.child_add_string("raid-status","")
xi10.child_add_string("state","")

xi15 = NaElement("aggr-snaplock-attributes")
xi1.child_add(xi15)

xi15.child_add_string("is-snaplock","")
xi15.child_add_string("snaplock-type","")

xi16 = NaElement("aggr-snapmirror-attributes")
xi1.child_add(xi16)

xi16.child_add_string("dp-snapmirror-destinations","")
xi16.child_add_string("ls-snapmirror-destinations","")
xi16.child_add_string("mv-snapmirror-destinations","")

xi17 = NaElement("aggr-snapshot-attributes")
xi1.child_add(xi17)

xi17.child_add_string("files-total","")
xi17.child_add_string("files-used","")
xi17.child_add_string("maxfiles-available","")
xi17.child_add_string("maxfiles-possible","")
xi17.child_add_string("maxfiles-used","")
xi17.child_add_string("percent-inode-used-capacity","")
xi17.child_add_string("percent-used-capacity","")
xi17.child_add_string("size-available","")
xi17.child_add_string("size-total","")
xi17.child_add_string("size-used","")

xi18 = NaElement("aggr-space-attributes")
xi1.child_add(xi18)

xi18.child_add_string("hybrid-cache-size-total","")
xi18.child_add_string("percent-used-capacity","")
xi18.child_add_string("size-available","")
xi18.child_add_string("size-total","")
xi18.child_add_string("size-used","")
xi18.child_add_string("total-reserved-space","")

xi19 = NaElement("aggr-striping-attributes")
xi1.child_add(xi19)

xi19.child_add_string("member-count","")

xi20 = NaElement("aggr-volume-count-attributes")
xi1.child_add(xi20)

xi20.child_add_string("flexvol-count","")
xi20.child_add_string("flexvol-count-collective","")
xi20.child_add_string("flexvol-count-not-online","")
xi20.child_add_string("flexvol-count-quiesced","")
xi20.child_add_string("flexvol-count-striped","")

xi21 = NaElement("aggr-wafliron-attributes")
xi1.child_add(xi21)

xi21.child_add_string("last-start-errno","")
xi21.child_add_string("last-start-error-info","")
xi21.child_add_string("scan-percentage","")
xi21.child_add_string("state","")
xi1.child_add_string("aggregate-name","")
xi1.child_add_string("aggregate-uuid","")

xi22 = NaElement("nodes")
xi1.child_add(xi22)

xi22.child_add_string("node-name","")
xi1.child_add_string("striping-type","")
api.child_add_string("max-records","2")

xi23 = NaElement("query")
api.child_add(xi23)

xi24 = NaElement("aggr-attributes")
xi23.child_add(xi24)

xi24.child_add_string("aggregate-name","aggr0*")
api.child_add_string("tag","")

xo = s.invoke_elem(api)
if (xo.results_status() == "failed") :
    print ("Error:\n")
    print (xo.sprintf())
    sys.exit (1)

print ("Received:\n")
print (xo.sprintf())
``` 

I can achieve the same results using less than **20** lines of XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<netapp  xmlns="http://www.netapp.com/filer/admin" version="1.32">
  <aggr-get-iter>
    <desired-attributes>
      <aggr-attributes>
        <aggregate-name></aggregate-name>
        <nodes>
          <node-name></node-name>
        </nodes>
      </aggr-attributes>
    </desired-attributes>
    <max-records>2</max-records>
    <query>
      <aggr-attributes>
        <aggregate-name>aggr0*</aggregate-name>
      </aggr-attributes>
    </query>
    <tag></tag>
  </aggr-get-iter>
</netapp>
```

And less than **50** lines of Python code:

```python
import sys
sys.path.append("/lib/python/NetApp")
from NaServer import *

s = NaServer("192.168.0.205", 1 , 32)
s.set_server_type("FILER")
s.set_transport_type("HTTPS")
s.set_port(443)
s.set_style("LOGIN")
s.set_admin_user("admin", "")

api = NaElement("aggr-get-iter")

xi = NaElement("desired-attributes")
api.child_add(xi)

xi1 = NaElement("aggr-attributes")
xi.child_add(xi1)

xi1.child_add_string("aggregate-name","")

xi2 = NaElement("nodes")
xi1.child_add(xi2)

xi2.child_add_string("node-name","")
api.child_add_string("max-records","2")

xi3 = NaElement("query")
api.child_add(xi3)

xi4 = NaElement("aggr-attributes")
xi3.child_add(xi4)

xi4.child_add_string("aggregate-name","aggr0*")
api.child_add_string("tag","")

xo = s.invoke_elem(api)
if (xo.results_status() == "failed") :
    print ("Error:\n")
    print (xo.sprintf())
    sys.exit (1)

print ("Received:\n")
print (xo.sprintf())
``` 

This is a massive reduction of code and complexity. It is a much welcomed result considering the fact that you'll likely be combining this code with other intelligence (e.g configuring MDS switches) and/or other API calls.

Further to this, even without any comments in the code it is clear what the latter script is achieving as opposed to the former script.

## Network Traffic

I'll now put my network engineer hat on for a moment to show what goes on behind the scenes with our API calls.

To make the information in my Wireshark captures more informative, I've connected to CDoT simulators through HTTP as opposed to HTTPS:

[![](/assets/2017/04/HTTP-1.png)](/assets/2017/04/HTTP-1.png)

_Please **do not** do this on production equipment though as it is completely insecure. Your username and password will be transmitted in clear text, as will all data that is requested and received._

Looking at the capture from our full, unedited query, we can see seven full sized packets (1514 bytes) being sent from the cluster to my PC:

[![](/assets/2017/04/Capture1.png)](/assets/2017/04/Capture1.png)

On the other hand, when we modify the request to include only the pieces of information we want, we don't even see one full sized packet:

[![](/assets/2017/04/Capture2.png)](/assets/2017/04/Capture2.png)

The difference between the two is massive.

You'd be forgiven for thinking that it isn't that much data in the grand scheme of things, but when you consider the fact that some of your remote sites might have high latency, low bandwidth and/or lossy connection, you begin to see the benefits of reducing the payload size.

## Treat APIs like SNMP

My final point on this subject is that you should treat APIs like SNMP. You wouldn't walk an entire SNMP tree just to get a device's hostname, nor should you make unnecessary API calls.