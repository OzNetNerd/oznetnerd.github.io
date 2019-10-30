---
title: Interacting with NetApp APIs, Part 1
sub_heading: "A series of posts to help you get started with NetApp APIs"
redirect_from: /interacting-with-netapp-apis-part-1/

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
If you're a regular reader of this blog, you'll see that I've been posting about [**automation**](/tags/#automation) and [**Python**](/category/python/) quite a lot recently. The reason being that it's not only fun, but I feel it's [**the way of the future**](/why-automate/). But I digress...

The reason for this post is to discuss my recent experience with NetApp's APIs. As I got off to a pretty slow start (which I feel was due to lack of documentation), I'll also provide setup and usage guidance in the hopes that you can get up and running sooner than I did.

## What do the NetApp APIs do?

To quote NetApp's documentation, _"You can create an application for seamless integration with NetApp solutions by using the ONTAP APIs. ONTAP APIs are available for security management, license management, backup and recovery, data replication, data archiving, and so on (see the following figure)."_

[![](/assets/2017/04/components-manage-ontap-sdk.png)](/assets/2017/04/components-manage-ontap-sdk.png)

## Getting Started

Now that we know what the APIs are capable of, let's get started. The first things you'll want to do are:

1.  [**Download the API documentation**](https://mysupport.netapp.com/documentation/productlibrary/index.html?productID=60427)
2.  [**Download the NetApp Manageability SDK**](http://mysupport.netapp.com/NOW/cgi-bin/software)

Now this is where things get tricky. At the time of writing, the latest version of the SDK is 5.6, as is the latest version of the documentation. However, the information contained in the documentation pertains only to Data ONTAP v9.0 & 9.1 which run ONTAPI versions 1.100 and 1.110 respectively. Because the simulators in my lab are running CDoT 8.3.2, the documentation does not meet my needs.

In order to obtain the correct information, I need to go back to the [**5.4 documentation**](https://mysupport.netapp.com/documentation/docweb/index.html?productID=62158&language=en-US) as it _"provides descriptions of all the APIs for Data ONTAP 8.3.1"._ It's not quite the version I'm running, but it's close enough.

So to recap, I'm running version 5.6 of the NetApp SDK **software**, but am reading version 5.4 of the NetApp SDK **documentation**.

Now that we've got that out of the way, extract both of the ZIP files to a location of your choosing.

## Documentation

Just a quick FYI, you'll need to use Internet Explorer or Edge when opening the  **"SDK_help.htm"** file because it won't work with Chrome.

Also, while the **"Type"** column tells you which entries are mandatory (e.g **'string'**) or optional (e.g **'integer option'**), this information can actually be misleading. I say this because if you've got a choice between two or more mandatory fields which achieve the same outcome, **both** fields will be marked as **optional**.

For example, the below screenshot is from the aggr-create  entry. It tells us that either disk-count  or disks  **must** be specified, yet they're both marked as **optional**.

The reason why NetApp would have done this is to avoid having users think they need to specify both, which would be the case if they were marked as mandatory. So while it might be a little confusing at first, it is definitely the right way to do it.

[![](/assets/2017/04/mandatory.png)](/assets/2017/04/mandatory.png)

## Cluster-Mode & Vserver APIs

As you'll see when you browse the documentation, as well as ZExplore (which will be discussed next), there's two types of API. **"Clustered"** and **"Vserver"**.

The **"Clustered"** API is used to issue commands which are related to the cluster as a whole, such as aggr . The **"Vserver"** API on the other hand is for commands which relate to SVMs, such as volume .

**Note:** It is important to remember that if you're using the **"Vserver"** API you need to connect to an SVM LIF. You will also need to use an account that has the required ONTAPI permissions.

## ZExplore Development Interface (ZEDI)

To quote [**NetApp's documentation**](https://community.netapp.com/fukiw75442/attachments/fukiw75442/developer-network-articles-and-resources/165/1/ZEDI+Detailed+User+Guide.pdf) again,

_"ZEDI is a utility with graphical user interface bundled with NetApp Manageability SDK (NMSDK). This utility enables you to test DATA ONTAP APIs and OnCommand Unified Manager APIs. This utility allows you to generate raw XML request for any given API._

_You can supply necessary arguments in the XML request before invoking the API through HTTP or HTTPS and you can view the response in raw XML format or tree format. Additionally, for a given API, the utility can generate sample codes in Java, Perl, C, C#, Python and Ruby to demonstrate how the said API can be invoked using NMSDK Core APIs._

_You can choose to include comments (API documentation descriptions) and/or optional parameters while generating the XML request and sample codes. You can generate workflows by sequencing multiple APIs in logical order. It also supports vFiler and Vserver tunneling, which enables a DATA ONTAP API to be invoked directly on a vFiler or a Vserver respectively."_

Now that you know what ZExplore does, let's take it for a quick spin. You can find it in the **"Zedi"** directory of the **"netapp-manageability-sdk"** ZIP file you downloaded earlier.

Here's what the **"Connect"** window looks like in my lab:

[![](/assets/2017/04/ZExplore.png)](/assets/2017/04/ZExplore.png)

**Note:** Upon running ZExplore, you might see the following error: _"ReferenceError: "importPackage" is not defined in at line number 2"._ Don't worry if you do, the tool appears to work just fine anyway.  Having said this, it might be worth rebooting your PC as that is what I did and haven't seen the error since.

After clicking **"Connect"**, I'm presented with the following message:

[![](/assets/2017/04/API_warning.png)](/assets/2017/04/API_warning.png)

The reason for this that CDoT 8.3.2 is running ONTAPI 1.32 but the version of Zexplore I'm running does not contain a 1.32 API document. This is interesting given that it **does **have documents for 1.100 and 1.110 (Data ONTAP v9.0 & 9.1 respectively). In other words, CDOT 8.3.1 and, 9 and 9.1 API documents are provided, but CDOT 8.3.2 are not. How odd! :)

Having said this though, it isn't too much of a problem. As mentioned by Andrew [**in this NetApp forum thread**](https://community.netapp.com/t5/Software-Development-Kit-SDK-and-API-Discussions/NMSDK-5-5-ZExplore-and-Ontapi-1-31-versus-1-32/td-p/122227):

_"Zexplore is simply lacking awareness of ZAPI 1.32. You can safely use version 1.31 for the vast majority of operations...you might encounter an occasional issue if there is a discrepancy between 1.31 and 1.32, where Zexplore is using the 1.31 specification but 1.32 differs in some way._

_If you're looking at the ZAPI docs, and not relying on Zexplore 100%, you'll be able to spot the differences."_

If you encounter a similar issue, what you'll need to do is select the closest API version from the dropdown menu located in the top left hand corner:

[![](/assets/2017/04/API_version.png)](/assets/2017/04/API_version.png)

This will ensure the greatest amount of compatibility between XExplore and the version of ONTAPI running on your controllers.

## Continue onto Part 2

In [**Part 2 of this series**](/interacting-netapp-apis-part-2/) I delve into ZExplore's GUI further and walk you through the process of making your first API call.