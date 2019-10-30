---
title: Getting started with Network Automation
sub_heading: "Automation: It's the way of the future"
redirect_from: /getting-started-network-automation/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- Python

tags:
- Ansible
- git
- Automation
- DevOps
---
I've been looking into network automation for quite some time now. Originally I started off looking into automating small tasks by writing Python scripts but then moved onto configuration management and orchestration tools.

While it might sound like a pretty straightforward path, it was actually quite an interesting  experience. In this post I'm going to share my thoughts in the hope that it assists others who are looking to get started in the network automation space.

## Version Control System

If you're anything like me when I first started out, when you write code your filenames look something like this:

*   Script.py
*   Script.py.back
*   Script-New.py
*   Script-New.py.back
*   Script-Final.py
*   Script-Final.y.back

As I'm sure you already know, doing things this way is a massive waste of time, it's messy and it's hard to maintain. Instead, you should use a VCS like git.

I won't get into the details of git because there is already a countless number of resources that cover it in depth, but just know that if you're interested in coding, you should look into a CVS sooner rather than later.

## Python Modules

When it comes to writing code, there is almost always a module or modules available which will can assist you in getting the job done quicker by allowing you to avoid reinventing the wheel.

For network automation, I suggest you look into the following modules:

*   **[Paramiko](http://www.paramiko.org/)**
*   **[Netmiko](https://github.com/ktbyers/netmiko)**

Both modules allow you to interact with network devices through SSH. You might find Netmiko easier to use because it is built on top of Paramiko and eases interaction with several vendors' products. (See the link above for a full list of supported vendors. )

## Configuration Management & Orchestration

If you're looking for a more complete solution, a configuration management and orchestration tool like Puppet, Chef, Ansible or Salt is what you're looking for.

For network automation, my suggestion would be to use Ansible. The reason being that Ansible is agentless whereas all of the other solutions require agents be installed on the devices which you want to manage.

While some vendors' products support the installation of agents, such as [**Cisco's Nexus series of switches**](https://puppet.com/product/managed-technology/cisco), many others don't, such as Cisco's Catalyst series. Therefore if you're looking for a tool that is compatible with all of your equipment, Ansible is the only way to go.