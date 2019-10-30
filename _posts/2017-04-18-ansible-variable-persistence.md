---
title: Ansible Variable Persistence
sub_heading: "Using static variables & dynamic inventory"
redirect_from: /ansible-variable-persistence/

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
*   **Reference: [Stackoverflow](http://stackoverflow.com/questions/29022767/using-ansible-set-fact-module-to-define-persistent-facts)**

[**set_fact**](/ansible-set_facts/) module only sets facts available during a run. For persistent facts, you'll need to either:

**Static:**

*   define them in one of the following:
    *   vars/
    *   group_vars/
    *   host_vars/

**Dynamic:**

*   Assign them to hosts using your language of choice via Ansible's Dynamic Inventory:
    *   http://docs.ansible.com/ansible/intro_dynamic_inventory.html
    *   http://docs.ansible.com/ansible/developing_inventory.html

The latter is what I usually choose to do, as it is quite simple to set up, and the facts are always available on all hosts, even if you are doing something like:

*   getting all the facts for all the hosts while connected to a nagios host in order to generate its configuration files.