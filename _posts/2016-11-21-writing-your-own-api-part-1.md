---
title: Writing your own API, Part 1
sub_heading: "Retrieve structured data from your network devices"
redirect_from: /writing-your-own-api-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg


categories:
- Ansible
- TextFSM
- Python

tags:
- Ansible
- Automation
- TextFSM
- DevOps
---
In this series of posts I will cover how you can create your own API-like functionality for devices which do not have APIs built into them. Before I get started though, for those of you who don't know what an API is, here's Wikipedia's description:

_"Just as a graphical user interface makes it easier for people to use programs, application programming interfaces make it easier for developers to use certain technologies in building applications. By abstracting the underlying implementation and **only exposing objects or actions** the developer needs, an API reduces the cognitive load on a programmer."_

For example, if you wanted a list of IP addresses on a large number of Cisco switches, you could issue the show ip int brief  command on each of them and then manually extract their IPs addresses. On the other hand, if the switches had an API you could use it to extract the IPs for you. This technique not only saves you time, but it also lends itself to automation and orchestration tasks perfectly.

The good news is that APIs are becoming increasingly popular in the networking space and you'll find a large number of vendors are releasing them for their product lines. The bad news is that (understandably) they're not releasing APIs for their older products. However, as with just about everything else in IT, there's always a solution to the problem.

The solutions I have come up with are listed below. They might look familiar as I mentioned them in my **[Getting started with Ansible](/getting-started-with-with-ansible/)** post too:

*   **[Paramiko](https://github.com/paramiko/paramiko)**
*   **[Netmiko](https://github.com/ktbyers/netmiko)**
*   **[NTC-Ansible](https://github.com/networktocode/ntc-ansible)**

While the first two are valid options, my preference is NTC-Ansible.  The reason being that, as its name suggests, it plugs into Ansible and therefore you've got your automation and orchestration piece already sorted. On the other hand, if you were to write scripts utilising the Paramiko and/or Netmiko libraries, you would then need to integrating them into Ansible.

(Note: NTC-Ansible actually uses Netmiko for its SSH connectivity.)

To provide API-like functionality, NTC-Ansible uses Google's [**TextFSM**](https://github.com/google/textfsm).  As per the github page:

It is a _"Python module for parsing semi-structured text into python tables."_

_"... implements a template based state machine for parsing semi-formatted text. Originally developed to allow programmatic access to information returned from the command line interface (CLI) of networking devices."_

_"A template file is needed for each uniquely structured text input."_

_"By developing a pool of template files, scripts can call TextFSM to parse useful information from a variety of sources."_

In a nutshell, you create template files which analyse your CLI output and return the information you specified, formatted in JSON - the same way that APIs do.

 For a great example, check out Jason Edelman's [**Getting Structured Data from Cisco IOS Devices using Ansible**](http://networktocode.com/labs/tutorials/getting-structured-data-from-cisco-ios-devices/) post. (Jason is the author of NTC-Ansible so it's no surprise he delivered a great demo of it. I have also provided a demonstration in **[Part 4 of this blog series.](/writing-your-own-api-part-4/)**)

The last line of Jason's post really sums everything up nicely:

_Since the return data from the network devices is now structured, we can use this data as inputs to other tasks in Ansible or in templates to create dynamic and real-time reports._

**[In my next post](/writing-your-own-api-part-2/)** I cover TextFSM in further detail as well as run through one of the built in examples.