---
title: Use Ansible Tags to Organise your Plays & Tasks
sub_heading: "Using tags to make life easier"
redirect_from: /use-ansible-tags-organise-plays-tasks/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- All Labs
- Ansible

tags:
- Ansible
- Automation
- DevOps
---
The **[Ansible tag documentation](http://docs.ansible.com/ansible/playbooks_tags.html)** does a great job of explaining tags, tag re-used and tag inheritance.  However, I'd like to dive a little deeper to show you just how useful they can be.

To get the most out of this post you'll need to be familiar with the ways in which you can structure your Playbooks. If you don't know how they can be structured, or if you need a refresher, I covered this topic in both my [**The Anatomy of an Ansible Playbook**](/anatomy-ansible-playbook/) and [**Ansible Playbook Structure**](/ansible-playbook-structure/) posts.

## Playbook Structure

For the purpose of this post, I'm using the following Playbook Layout:

```
.
├── group_vars
│   └── all.yml
├── hosts.ini
├── roles
│   ├── routers
│   │   └── tasks
│   │       ├── main.yml
│   │       ├── sh_ip_bgp.yml
│   │       └── sh_ip_route.yml
│   └── switches
│       └── tasks
│           ├── main.yml
│           ├── sh_mac.yml
│           └── sh_stp.yml
├── rtr.yml
├── site.yml
└── swh.yml
```

Let's now go ahead and break down each component of this structure.

## site.yml

As per the **[Ansible best practices documentation](http://docs.ansible.com/ansible/playbooks_best_practices.html)**:

_In site.yml, we include a playbook that defines our entire infrastructure. Note this is SUPER short, because it’s just including some other playbooks._

```yaml
{% raw %}---{% endraw %}
- hosts: all
  gather_facts: False

- include: rtr.yml
  tags: Router_Tasks

- include: swh.yml
  tags: Switch_Tasks

```

We see two things happening in "**site.yml"**:

1.  The **"rtr.yml"** and **"swh.yml"** Playbooks are being included as per the quoted documentation above.
2.  We see a tag being applied to each of the Playbooks.

As mentioned in the **[Ansible tag documentation](http://docs.ansible.com/ansible/playbooks_tags.html)**, tags are inherited. This means that all Playbooks and Tasks included inside of the **"rtr.yml"** and **"swh.yml"** Playbooks will inherit these tags.

## rtr.yml & swh.yml

These two Playbook files simply "include" the roles which they pertain to -

`rtr.yml`:

```yaml
- hosts: all
  connection: local
  roles:
    - routers
```

`swh.yml`

```yaml
- hosts: all
  connection: local
  roles:
    - switches
```

Note that an alternative option would be to not tag at the**"site.yml"** Playbook level and instead tag at the **"rtr.yml"** and **"swh.yml"** Playbook role level instead. For example, instead of doing the above, your **"rtr.yml"** could look like this instead:

```yaml
- hosts: all
  connection: local
  roles:
    - {role: routers_config, tags: ['routers_config']}
    - {role: routers_show, tags: ['routers_show']}
```

As you can see, this affords you more flexibility because your **"show"** and **"configuration"** commands will be contained in different directories and will be tagged differently too.

## main.yml files

Similar to the Playbook files covered in the previous section, the two **"main.yml"** files "include" the Task files they pertain to -

`roles/routers/tasks/main.yml`:

```yaml
{% raw %}---{% endraw %}
- include: sh_ip_bgp.yml
  tags: [show_ip_bgp]

- include: sh_ip_route.yml
  tags: [show_ip_route]
```

`roles/switches/tasks/main.yml`:

```yaml
{% raw %}---{% endraw %}
- include: sh_mac.yml
  tags: [show_mac_table]

- include: sh_stp.yml
  tags: [show_span]
```

Note that this time I have applied applied tags.

## Task files

Because the Task files are almost identical, I'll only cover one from each Playbook - **"sh_ip_route.yml"** and **"sh_mac.yml"**.

`roles/routers/tasks/sh_ip_route.yml`:

```yaml
{% raw %}---{% endraw %}
- name: Run the 'show ip route' command
  ios_command:
    provider: "{{ provider }}"
    commands:
      - show ip route
  register: show_out

- name: Display the 'show ip route' command output
  debug: var=show_out.stdout_lines
  tags: DEBUG
```

`roles/switches/tasks/sh_mac.yml`

```yaml
{% raw %}---{% endraw %}
- name: Run the 'show mac-address-table' command
  ios_command:
    provider: "{{ provider }}"
    authorize: yes
    commands:
      - show mac-address-table
  register: show_out

- name: Display the 'show mac-address-table' command output
  debug: var=show_out.stdout_lines
  tags: DEBUG
```

## Bringing it all Together

Now that we've got all of our tags applied, let's see how we can use them to make our lives easier.

Using the --list-tasks  option we can see a list of all of the Tasks contained in the **sh_ip_bgp.yml, sh_ip_route.yml, sh_mac.yml** and **sh_stp.yml** files:

```
$ ansible-playbook -i hosts.ini  site.yml --list-tasks

playbook: site.yml

  play #1 (all): all	TAGS: []
    tasks:

  play #2 (all): all	TAGS: [Router_Tasks]
    tasks:
      routers : Run the 'show ip bgp' command	TAGS: [Router_Tasks, show_ip_bgp]
      routers : Display the 'show ip bgp' command output	TAGS: [DEBUG, Router_Tasks, show_ip_bgp]
      routers : Run the 'show ip route' command	TAGS: [Router_Tasks, show_ip_route]
      routers : Display the 'show ip route' command output	TAGS: [DEBUG, Router_Tasks, show_ip_route]

  play #3 (all): all	TAGS: [Switch_Tasks]
    tasks:
      switches : Run the 'show mac-address-table' command	TAGS: [Switch_Tasks, show_mac_table]
      switches : Display the 'show mac-address-table' command output	TAGS: [DEBUG, Switch_Tasks, show_mac_table]
      switches : Run the 'show spanning-tree' command	TAGS: [Switch_Tasks, show_span]
      switches : Display the 'show spanning-tree' command output	TAGS: [DEBUG, Switch_Tasks, show_span]
```

_Note that as a result of us breaking the Tasks up into roles we can see that they're neatly displayed in their **"routers"** and **"switches"** Playbooks respectively. While this is very useful, it has nothing to to do with tags._

### Filtering Tasks

Let's say we're interested in looking at the Router tasks only. We can view them by combining the --list-tasks  and --tags  options:

```
$ ansible-playbook -i hosts.ini  site.yml --list-tasks --tags Router_Tasks

playbook: site.yml

  play #1 (all): all	TAGS: []
    tasks:

  play #2 (all): all	TAGS: [Router_Tasks]
    tasks:
      routers : Run the 'show ip bgp' command	TAGS: [Router_Tasks, show_ip_bgp]
      routers : Display the 'show ip bgp' command output	TAGS: [DEBUG, Router_Tasks, show_ip_bgp]
      routers : Run the 'show ip route' command	TAGS: [Router_Tasks, show_ip_route]
      routers : Display the 'show ip route' command output	TAGS: [DEBUG, Router_Tasks, show_ip_route]

  play #3 (all): all	TAGS: [Switch_Tasks]
    tasks:
```

Excellent, we've achieved what we set out to do. However, because our ios_command  and debug  Tasks are named so similarly, having them both listed is redundant. Let's add the --skip-tags  option to the command above:

```
$ ansible-playbook -i hosts.ini  site.yml --list-tasks --tags Router_Tasks --skip-tags DEBUG

playbook: site.yml

  play #1 (all): all	TAGS: []
    tasks:

  play #2 (all): all	TAGS: [Router_Tasks]
    tasks:
      routers : Run the 'show ip bgp' command	TAGS: [Router_Tasks, show_ip_bgp]
      routers : Run the 'show ip route' command	TAGS: [Router_Tasks, show_ip_route]

  play #3 (all): all	TAGS: [Switch_Tasks]
    tasks:
```

And there we have it! A nice, clean list of Router_Tasks .

### Running Specific Tasks

The next step is to run the specific Task(s) we're interested in. To do this we simply use the same commands above but with the --list-tasks  option removed:

```yaml
$ ansible-playbook -i hosts.ini  site.yml --tags Router_Tasks 

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

TASK [routers : Run the 'show ip bgp' command] *********************************
ok: [R2]
ok: [R1]

TASK [routers : Display the 'show ip bgp' command output] **********************
ok: [R1] => {
    "show_out.stdout_lines": [
        [
            "% BGP not active", 
            ""
        ]
    ]
}
ok: [R2] => {
    "show_out.stdout_lines": [
        [
            "% BGP not active", 
            ""
        ]
    ]
}

TASK [routers : Run the 'show ip route' command] *******************************
ok: [R2]
ok: [R1]

TASK [routers : Display the 'show ip route' command output] ********************
ok: [R1] => {
    "show_out.stdout_lines": [
        [
            "Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP", 
            "       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area ", 
            "       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2", 
            "       E1 - OSPF external type 1, E2 - OSPF external type 2", 
            "       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2", 
            "       ia - IS-IS inter area, * - candidate default, U - per-user static route", 
            "       o - ODR, P - periodic downloaded static route", 
            "", 
            "Gateway of last resort is not set", 
            "", 
            "C    192.168.0.0/24 is directly connected, FastEthernet0/0"
        ]
    ]
}
ok: [R2] => {
    "show_out.stdout_lines": [
        [
            "Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP", 
            "       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area ", 
            "       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2", 
            "       E1 - OSPF external type 1, E2 - OSPF external type 2", 
            "       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2", 
            "       ia - IS-IS inter area, * - candidate default, U - per-user static route", 
            "       o - ODR, P - periodic downloaded static route", 
            "", 
            "Gateway of last resort is not set", 
            "", 
            "C    192.168.0.0/24 is directly connected, FastEthernet0/0"
        ]
    ]
}

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

PLAY RECAP *********************************************************************
R1                         : ok=7    changed=0    unreachable=0    failed=0   
R2                         : ok=7    changed=0    unreachable=0    failed=0
```

While that worked it produced a lot of output. We can avoid this by using the  --skip-tags  option:

```yaml
$ ansible-playbook -i hosts.ini  site.yml --tags Router_Tasks --skip-tags DEBUG

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

TASK [routers : Run the 'show ip bgp' command] *********************************
ok: [R1]
ok: [R2]

TASK [routers : Run the 'show ip route' command] *******************************
ok: [R1]
ok: [R2]

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

PLAY RECAP *********************************************************************
R1                         : ok=5    changed=0    unreachable=0    failed=0   
R2                         : ok=5    changed=0    unreachable=0    failed=0
```

Note that the output can be further reduced by specifying the **"rtr.yml"** Playbook directly instead of **"site.yml"**:

```yaml
$ ansible-playbook -i hosts.ini  rtr.yml --tags Router_Tasks --skip-tags DEBUG

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [R1]
ok: [R2]

PLAY RECAP *********************************************************************
R1                         : ok=1    changed=0    unreachable=0    failed=0   
R2                         : ok=1    changed=0    unreachable=0    failed=0
```

This is perfectly fine to do because all of the Tasks we're interested in are contained inside of the role which the **"rtr.yml"** Playbook has access to.