---
title: The Anatomy of an Ansible Playbook
sub_heading: "How to structure your Ansible code cleanly"
redirect_from: /anatomy-ansible-playbook/

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
I published my [**Ansible Playbook Structure**](/ansible-playbook-structure/) post a few months ago. Upon re-reading it recently I felt that it lacked detailed around the components of a playbook. I therefore intend to fill in the gap with this post.

## 30,000 Foot View

Ansible uses the following components to work:

*   Inventory file(s)
*   Group vars
*   Host vars
*   Playbooks
*   Roles
*   Tasks

I'll delve into each of these in a moment, but a high level explanation is as follows -

When you run an Ansible **Playbook** (e.g ansible-playbook -i hosts.ini site.yml ), you specify the **Inventory** file location (-i hosts.ini ) and the **Playbook** you want to run (site.yml) ).

**Inventory** file(s) contains a list of hosts which you want Ansible to interact with. **Group** **vars** and **Host vars** pertain to the **Groups** and **Hosts** specified in the **Inventory** file(s).

The **Playbook **can have links to **roles** and/or other **Playbooks**. The **roles** contain lists of **tasks** which will perform the work you've configured them to.

Now that we've got the high level overview out of the way, let's dive deeper!

## Directory Layout

As mentioned in the **[Ansible Playbook Structure](/ansible-playbook-structure/)** post, the layout which I've chosen to use is:

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

And here is what my layout looks like:

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

## Inventory (Hosts) file

As mentioned above, the Inventory file (also known as the Hosts file), contains a list of hosts which Ansible will be interacting with. The file can be as basic as containing a list of hostnames, or it could be as complex as containing groups, groups inside other groups, host variables, etc.

My advice is to keep your Inventory file as simple as possible so as to avoid unnecessary confusion.

Here's an example of what a basic Inventory file looks like:

```
will@ubuntu:~/all/Ansible$ cat hosts.ini 
[gns3]
10.255.0.254
10.255.2.2
R3 ansible_host=10.255.3.3
```

In this Inventory file we see three things:

1.  Three hosts being specified.
2.  All three hosts belong to a group called **"gns3"**.
3.  **"R3"** has a host variable specified for it.

The default location for the Inventory file is /etc/ansible/hosts . However, you can create as many Inventory files as you like and store them wherever you like, so long as you specify its location with the -i  flag when you run a Playbook. For example:

```
ansible-playbook -i /path/to/inventory/hosts.ini site.yml
```

## Group Variables

Group variables as the name suggests, applies to all of the hosts which reside inside a group. There is also an **"all"** group variables file which specifies variables for all groups. To configure group variables simply create a YAML file which matches the name of the group.

For example, in the **"Inventory"** section above we saw three hosts had been added to the **"gns3"** group inside of the **"hosts.ini"** file. We also saw that there were **"gns3.yml"  **and **"all.yml"**files located under the **"group_vars"** directory in the second output of the **"Directory Layout"** section. In other words, what we've got at this point is:

```
├── hosts.ini
├── group_vars
    ├── all.yml
    ├── gns3.yml
```

Let's now take a look at the contents of these two files:

**all.yml:**

```
{% raw %}---{% endraw %}
 TEST_DIR: "./tests"
```

**gns3.yml:**

```
{% raw %}---{% endraw %}
#required for ntc_show_command
username: cisco
password: cisco
auth_pass: cisco

#required for ios_config
provider:
host: "{{ inventory_hostname }}"
username: cisco
password: cisco
auth_pass: cisco
```

## Host Variables

Host variables work the same way as Group variables, but instead of the filename matching the group's name, it needs to match the host's name.

## Playbooks

Playbooks are the heart of Ansible. They can contain other Playbooks, roles and/or tasks. When you want Ansible to action something, you run a Playbook and it gets the job done. Below is an example of a Playboook which calls two roles:

```yaml
will@ubuntu:~/all/Ansible$ cat gns3.yml 

---
- hosts: gns3
  gather_facts: no
  connection: local
  roles:
    - common
    - gns3
```

## Roles

As the [**Ansible documentation**](http://docs.ansible.com/ansible/playbooks_roles.html#roles) explains,

_Roles are ways of **automatically loading certain vars_files, tasks, and handlers** based on a known file structure._

_Roles are just automation around ‘include’ directives as described above, and really don’t contain much additional magic beyond some improvements to search path handling for referenced files. However, that can be a big thing!_

_This designates the following behaviors, for each role **‘x’:**_

*   _If roles/x/tasks/main.yml  exists, tasks listed therein will be added to the play_
*   _If roles/x/handlers/main.yml  exists, handlers listed therein will be added to the play_
*   _If roles/x/vars/main.yml  exists, variables listed therein will be added to the play_
*   _If roles/x/defaults/main.yml  exists, variables listed therein will be added to the play_
*   _If roles/x/meta/main.yml  exists, any role dependencies listed therein will be added to the list of roles (1.3 and later)_
*   _Any copy, script, template or include tasks (in the role) can reference files in roles/x/{files,templates,tasks}/ (dir depends on task) without having to path them relatively or absolutely_

Given that we included the **"common"** and **"gns3"** roles, replacing the **"x"'**s in the above directory paths would give you the locations which would be used by this Playbook.

**Note:** I urge you to **[read the Ansible documentation](http://docs.ansible.com/ansible/playbooks_roles.html)** on roles because there is quite a lot more to them than what I've written above.

### Common role

The **Common** role is used to store tasks which can be used across all of your Playbooks. Some suggest not using it because they feel it can be confusing/unnecessary. However, in my opinion it's very useful.

For example, if I've got commands which are common across multiple roles (e.g the **"router"** and **"switch"** roles), I'd use the **Common** role. That way I won't have to duplicate the commands between the "router" and "switch" roles, I would only have to configure them in one location.

## Tasks

As mentioned in the Roles section above, the roles/common/tasks/main.yml and roles/gns3/tasks/main.yml directories (among others) are loaded automatically when this Playbook is run because they their roles (common and gns3 respectively) were called in the Playbook's configuration file.

Let's now take a look at the contents of roles/gns3/tasks/main.yml :

```yaml
{% raw %}---{% endraw %}
- name: ntc_show - Find CDP neighbors
  ntc_show_command:
    connection: ssh
    platform: cisco_ios
    command: "show cdp neighbor"
    host: "{{ inventory_hostname }}"
    username: "{{ username }}"
    password: "{{ password }}"
  register: results
  tags: ntc_show_cdp

- name: DEBUG ntc_show - Find CDP neighbors
  debug: var=results
  tags: ntc_show_cdp
```

Note that the {% raw %} "{{ username }}" {% endraw %} and {% raw %} "{{ password }}" {% endraw %} variables are pulled from the group_vars/gns3.yml file we saw earlier, while "{{ inventory_hostname }}"  is a built in Ansible variable which refers to the hosts you're running the Playbook against.

For example, running the following command:

```
ansible-playbook -i hosts.ini -l gns3 site.yml
```
would run the Playbook against the 10.255.0.254, 10.255.2.2 and 10.255.3.3 (the hosts which are in the **"gns3"** group) and it would replace the {% raw %} "{{ inventory_hostname }}" {% endraw %} with each of the hosts' IP addresses.