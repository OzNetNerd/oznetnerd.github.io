---
title: Ansible Playbook Structure
sub_heading: "Keeping things tidy and usable"
redirect_from: /ansible-playbook-structure/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Labs
- Ansible

tags:
- Ansible
- Automation
- DevOps
---
**Update: If you find this article useful, I suggest reading the [Anatomy of an Ansible Playbook](/anatomy-ansible-playbook) post as well.**

Recently I posted about how it can be easy to [**get lost in the sea of options**](/writing-your-own-api-part-1/) when it comes to having Ansible control your network gear. The next challenge you may face is figuring out how to structure your playbooks.

For example, you can list your hosts, as well as your host and group variables in the [**/etc/ansible/hosts**](http://docs.ansible.com/ansible/intro_inventory.html) file, or you can create your own inventory, host and group files in your [**directory layout**](http://docs.ansible.com/ansible/playbooks_best_practices.html#directory-layout). Further to this, talking about variables alone for a moment, there are [**sixteen different places**](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) they can be specified.

_Note that the hosts file is known as the [**inventory**](http://docs.ansible.com/ansible/intro_inventory.html) file._

Another example is if you want to use custom modules. You can edit the **[module search path](https://github.com/networktocode/ntc-ansible)** or you can put the modules in your [**directory layout**](http://docs.ansible.com/ansible/playbooks_best_practices.html#directory-layout).

## Directory Layout

So which method do you choose? Well, whichever suits your needs best. At the time of writing I've found that the first directory layout on [**Ansible's Best Practices page**](http://docs.ansible.com/ansible/playbooks_best_practices.html#directory-layout) (pasted below for convenience) a great fit for me.

```
production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1                 # here we assign variables to particular groups
   group2                 # ""
host_vars/
   hostname1              # if systems need specific variables, put them here
   hostname2              # ""

library/                  # if any custom modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  
```

Using [**tree**](http://manpages.ubuntu.com/manpages/wily/man1/tree.1.html) is a great way to keep track of what your layout looks like:

```
will@ubuntu:~/all/Ansible$ tree -I "ntc-templates|library"
.
├── filter_plugins
│   ├── split.py
│   └── split.pyc
├── gns3.yml
├── group_vars
│   ├── all.yml
│   ├── cisco_ios.yml
│   ├── cisco_nxos.yml
│   ├── gns3.yml
│   └── hp_comware.yml
├── hosts.ini
├── host_vars
├── roles
│   ├── common
│   │   ├── defaults
│   │   ├── files
│   │   ├── handlers
│   │   ├── meta
│   │   ├── tasks
│   │   ├── templates
│   │   └── vars
│   └── gns3
│       ├── defaults
│       ├── files
│       ├── handlers
│       ├── meta
│       ├── tasks
│       │   ├── main.yml
│       │   ├── NTC_cdp.yml
│       │   ├── NTC_config_int.yml
│       │   ├── NTC_show_cdp.yml
│       │   ├── show_vers_ACL.yml
│       │   └── show_vers.yml
│       ├── templates
│       └── vars
├── site.retry
└── site.yml

20 directories, 17 files
```

_Note that I used a couple of filters with my 'tree' command because I'm using [**NTC-Ansible**](https://github.com/networktocode/ntc-ansible) which contains a large amount of files which do not need to be seen for the purpose of this post._

## Configuration Breakdown

Now that we've seen the directory layout, let's see what configurations go into which files.

As the name suggests, I've got my hosts listed in the hosts.ini file as opposed to the /etc/ansible/hosts file. The contents of my hosts.ini file is as follows:

```
will@ubuntu:~/all/Ansible$ cat hosts.ini 
[gns3]
10.255.0.254
10.255.2.2
10.255.3.3
```

What we see here is three hosts specified under the gns3 group. This means that any playbooks we run against the gns3 group will run against these three devices. It also means that any variables which are define in the group_vars/gns3.yml (covered later in this post) will be made available to these three devices.

It is important to remember that because the default inventory file (/etc/ansible/hosts) is not being used, we need to specify the location of the inventory file we are using when running the playbook. For example, in the command below we're telling Ansible to use the hosts.ini file:

will@ubuntu:~/all/Ansible$ ansible-playbook -i hosts.ini site.yml

Now, in the above command we see a reference to site.yml. Let's take a look at its contents:

```
will@ubuntu:~/all/Ansible$ cat site.yml 
---
- include: gns3.yml 
```

All this file does is it does links to playbooks. Why you ask? Well, as per the [**Ansible documentation**](http://docs.ansible.com/ansible/playbooks_best_practices.html), _"the idea here is that we can choose to configure our whole infrastructure by 'running' site.yml or we could just choose to run a subset..."._

We can see that site.yml contains a reference to gns3.yml, so let's now look at its contents:

```yaml
will@ubuntu:~/all/Ansible$ cat gns3.yml 
---
- hosts: gns3
  gather_facts: no
  connection: local
  roles:
    - common
    - gns3

  tasks:
  - include: roles/gns3/tasks/show_vers.yml
```

OK so now we're getting somewhere. We can see that this playbook is retrieving its configuration from the common and gns3 roles, and its tasks from roles/gns3/tasks/show_vers.yml. _(Note that by default it will automatically pull its tasks from roles/gns3/tasks/main.yml as well.)_

Let's follow the bouncing ball and take a look at the contents of the show_vers.yml file:

```yaml
will@ubuntu:~/all/Ansible$ cat roles/gns3/tasks/show_vers.yml 
---
  - name: RUN 'sho cdp neighbors'
    ios_command:
      provider: "{{ provider }}"
      commands:
        - show cdp neighbors
    register: neighbors

  - name: DEBUG 'show cdp neighbors'
    debug: var=neighbors
```

OK now we see a reference to the {% raw %} "{{ provider }}" {% endraw %} variable. As I mentioned earlier, any device in the gns3 group (specified in the hosts.ini file) will have access to the variables contained in the the group_vars/gns3.yml file. Therefore let's go ahead and look at its contents:

```yaml
provider:
  host: "{{ inventory_hostname }}"
  username: cisco
  password: cisco
  auth_pass: cisco
```

As we can see, there's nothing too fancy here. All we have is handful of variables and their values. The important thing to note here though is that, as mentioned a couple of times in this post already, this file is bound to the gns3 group specified in the hosts.ini file. This binding is done automatically due to the group name matching the group_vars filename.

## Bringing it all Together

To summarise what we've seen above:

*   site.yml - Contains links to all of our playbooks.
*   hosts.ini - Contains a list of hosts and groups of hosts that we want our playbooks to run against.
*   group_vars/gns3.yml - Contains all of the variables which can be used by the hosts specified in the gns3 group (see hosts.ini).
*   gns3.yml - This is a playbook which contains links (includes) to the tasks in the roles/gns3/tasks directory. The roles/gns3/tasks/main.yml file is implicitly added to this playbook.

This modularity affords us a great amount of flexibility. This method requires a lot less administration than would be required if for example, the hostnames and variables were specified in each and every playbook.

## Ansible Galaxy

If the above approach doesn't suit your needs and/or you don't want to build the roles yourself, head over to [**Ansible Galaxy**](https://galaxy.ansible.com/). The site allows members of the community share their role configurations with one another, free of charge. There are so many there you're bound to find one that does exactly what you need.

**Update: If you find this article useful, I suggest reading the [Anatomy of an Ansible Playbook](/anatomy-ansible-playbook) post as well.**