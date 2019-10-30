---
title: Ansible Installation
sub_heading: "It's as easy as running four commands"
redirect_from: /ansible-installation/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Ansible
- Automation
- DevOps
---
**Reference: [Ansible](http://docs.ansible.com/ansible/intro_installation.html)**

To configure the PPA on your machine and install Ansible run these commands:

```bash
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```