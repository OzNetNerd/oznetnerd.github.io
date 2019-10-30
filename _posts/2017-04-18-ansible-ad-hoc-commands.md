---
title: Ansible Ad-Hoc Commands
sub_heading: "Running standalone commands"
redirect_from: /ansible-ad-hoc-commands/

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
As per the [**Ansible documentation**](http://docs.ansible.com/ansible/intro_adhoc.html):

_An ad-hoc command is something that you might type in to do something really quick, but don’t want to save for later._

For example, if you want to you can test a module, (e.g [**IOS_Facts**](https://docs.ansible.com/ansible/ios_facts_module.html)), without having to write a Playbook by using [**Ad-Hoc commands**](http://docs.ansible.com/ansible/intro_adhoc.html) such as this:

```bash
ansible all -i 192.168.0.210, -c local -m ios_facts -a "host=192.168.0.210 username=cisco password=cisco gather_subset=hardware"
```

Here is another example, this time using the [**IOS_command**](https://docs.ansible.com/ansible/ios_command_module.html) module:

```bash
ansible all -i 192.168.0.210, -c local -m ios_command -a "host=192.168.0.210 username=cisco password=cisco auth_pass=cisco authorize=true commands='show run'"
```