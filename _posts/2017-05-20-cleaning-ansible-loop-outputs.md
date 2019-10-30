---
title: Cleaning Up Ansible Loop Outputs
sub_heading: "A clean output is a good output"
redirect_from: /cleaning-ansible-loop-outputs/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Network Automation
- Ansible
- Automation
- DevOps
- Jinja2
---

_Note that this post uses NTC-Ansible. Installation instructions can be found [**here.**](/installing-ntc-ansible/)_

As we saw in the **[--extra-vars](/ansible-extra-vars/)** post, the first Playbook produces a very clean, succinct output. However, after making a minor alteration to the Playbook (I added a single loop and one additional show command), the output went from just 43 lines to over 260 lines!

While there is nothing functionally wrong with the way that Ansible displays its loop output, it is distracting and difficult to find the information you're looking for. We can resolve this "issue" by using the default()  **[Jinja2 template](/tags/#jinja2)**, like so:

`show_tidy.yml`:

```yaml
{% raw %}
- hosts: "{{ hosts }}"
  connection: local
  gather_facts: no

  vars:
    provider:
      host: "{{ ansible_host }}"
      username: "{{ username }}"
      password: "{{ password }}"
      platform: "{{ platform }}"

  tasks:
  - name: ntc_show
    ntc_show_command:
      provider: "{{ provider }}"
      connection: "{{ connection }}"
      command: "{{ item }}"
      secret: "{{ secret }}"
    with_items:
      - "{{ command }}"
    register: results

  - name: Clean loop output
    set_fact:
      clean_out: "{{ clean_out|default([]) + [item.response] }}"
    with_items:
      - "{{ results.results }}"
    no_log: True

  - name: Display information from 'show' output
    debug: msg="{{ clean_out }}"
    tags: DEBUG
{% endraw %}
```

Which results in the following output:

`show_tidy.yml`:

```yaml
{% raw %}
$ ansible-playbook show_tidy.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show ip interface brief']"

PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R2] => (item=show version)
ok: [R1] => (item=show ip interface brief)
ok: [R2] => (item=show ip interface brief)

TASK [Clean loop output] *******************************************************
ok: [R1] => (item=(censored due to no_log))
ok: [R1] => (item=(censored due to no_log))
ok: [R2] => (item=(censored due to no_log))
ok: [R2] => (item=(censored due to no_log))

TASK [Display information from 'show' output] **********************************
ok: [R1] => {
    "msg": [
        [
            {
                "config_register": "0x2102",
                "hardware": [
                    "3745"
                ],
                "hostname": "R1",
                "rommon": "ROMMON",
                "running_image": "//255.255.255.255/unknown",
                "serial": [
                    "FTX0945W0MY"
                ],
                "uptime": "2 hours, 32 minutes",
                "version": "12.4(15)T14"
            }
        ],
        [
            {
                "intf": "FastEthernet0/0",
                "ipaddr": "192.168.0.210",
                "proto": "up",
                "status": "up"
            },
            {
                "intf": "FastEthernet0/1",
                "ipaddr": "unassigned",
                "proto": "down",
                "status": "administratively down"
            }
        ]
    ]
}
ok: [R2] => {
    "msg": [
        [
            {
                "config_register": "0x2102",
                "hardware": [
                    "3745"
                ],
                "hostname": "R2",
                "rommon": "ROMMON",
                "running_image": "//255.255.255.255/unknown",
                "serial": [
                    "FTX0945W0MY"
                ],
                "uptime": "2 hours, 32 minutes",
                "version": "12.4(15)T14"
            }
        ],
        [
            {
                "intf": "FastEthernet0/0",
                "ipaddr": "192.168.0.197",
                "proto": "up",
                "status": "up"
            },
            {
                "intf": "FastEthernet0/1",
                "ipaddr": "unassigned",
                "proto": "down",
                "status": "administratively down"
            }
        ]
    ]
}

PLAY RECAP *********************************************************************
R1                         : ok=3    changed=0    unreachable=0    failed=0
R2                         : ok=3    changed=0    unreachable=0    failed=0
{% endraw %}
```

Isn't it beautiful!? It's as succinct as the output before the introduction of the loop and is down from 261 to 89 lines.

Now let's take a moment to dissect what the Clean loop output Task does, and more specifically, what the default()  Jinja template does. [**As per the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#default):

default(value, default_value=u'', boolean=False)

_If the value is undefined it will return the passed default value, otherwise the value of the variable._

What this means is that if clean_out  is undefined, it will get defined as an empty list as per the []  in the filter: `{% raw %}clean_out: "{{ clean_out|default([]) + [item.response] }}"{% endraw %}`  - however, if clean_out  is defined then Jinja will keep its original value intact.

You might be wondering why it's even necessary to use the default([])  filter. Well it is because if we don't Ansible would throw an "undefined variable" error. This is because clean_out  does not exist until we try to append the first item in the loop to it, however, we can't append the item until clean_out  has been created. This ensures that that happens. An alternative would be to create clean_out  in a separate Task, however, it's cleaner and easier to manage using this method.

Finally the last part of the filter, \+ [item.response]  simply appends the next loop item to the clean_out  list. Once all items have been looped through they will be stored in clean_out  and will be ready for displaying/outputting.

## Git

This playbook is [**available here**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/Jinja/default/show_tidy.yml) in my Github repo.