---
title: Looping through Dictionaries in Ansible
sub_heading: "Let's get loopy"
redirect_from: /looping-dictionaries-ansible/

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
---
_Note that this post uses NTC-Ansible. Installation instructions can be found [**here.**](/installing-ntc-ansible/)_

In the **[Understanding Ansible Output Structure](/understanding-ansible-output-structure/)** post we saw how to extract a single entry out of a dictionary. We saw that it's possible to do it using the [**map() filter**](/jinja2-map-filter/) too. However, what if we want to extract all entries in the dictionary, how would we go about that? The answer is simple - use with_dict . For example, this Playbook:

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
    register: output

  - name: Display output
    debug: msg="The key is {{ item.key }} and the value is {{ item.value }}"
    with_dict: "{{ output.results[1].response[0] }}"
{% endraw %}
```

Produces the following output:

```yaml
{% raw %}
$  ansible-playbook loop_dict.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show cdp neighbor detail']"

PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R2] => (item=show version)
ok: [R1] => (item=show version)
ok: [R2] => (item=show cdp neighbor detail)
ok: [R1] => (item=show cdp neighbor detail)

TASK [Display output] **********************************************************
ok: [R1] => (item={'key': u'software_version', 'value': u'Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)'}) => {
    "item": {
        "key": "software_version",
        "value": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
    },
    "msg": "The key is software_version and the value is Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
}
ok: [R1] => (item={'key': u'local_port', 'value': u'FastEthernet0/0'}) => {
    "item": {
        "key": "local_port",
        "value": "FastEthernet0/0"
    },
    "msg": "The key is local_port and the value is FastEthernet0/0"
}
ok: [R1] => (item={'key': u'remote_port', 'value': u'FastEthernet0/0'}) => {
    "item": {
        "key": "remote_port",
        "value": "FastEthernet0/0"
    },
    "msg": "The key is remote_port and the value is FastEthernet0/0"
}
ok: [R1] => (item={'key': u'destination_host', 'value': u'R2.lab'}) => {
    "item": {
        "key": "destination_host",
        "value": "R2.lab"
    },
    "msg": "The key is destination_host and the value is R2.lab"
}
ok: [R1] => (item={'key': u'management_ip', 'value': u'192.168.0.197'}) => {
    "item": {
        "key": "management_ip",
        "value": "192.168.0.197"
    },
    "msg": "The key is management_ip and the value is 192.168.0.197"
}
ok: [R1] => (item={'key': u'platform', 'value': u'Cisco 3745'}) => {
    "item": {
        "key": "platform",
        "value": "Cisco 3745"
    },
    "msg": "The key is platform and the value is Cisco 3745"
}
ok: [R2] => (item={'key': u'remote_port', 'value': u'FastEthernet0/0'}) => {
    "item": {
        "key": "remote_port",
        "value": "FastEthernet0/0"
    },
    "msg": "The key is remote_port and the value is FastEthernet0/0"
}
ok: [R2] => (item={'key': u'software_version', 'value': u'Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)'}) => {
    "item": {
        "key": "software_version",
        "value": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
    },
    "msg": "The key is software_version and the value is Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
}
ok: [R2] => (item={'key': u'platform', 'value': u'Cisco 3745'}) => {
    "item": {
        "key": "platform",
        "value": "Cisco 3745"
    },
    "msg": "The key is platform and the value is Cisco 3745"
}
ok: [R2] => (item={'key': u'destination_host', 'value': u'R1.lab.local'}) => {
    "item": {
        "key": "destination_host",
        "value": "R1.lab.local"
    },
    "msg": "The key is destination_host and the value is R1.lab.local"
}
ok: [R2] => (item={'key': u'local_port', 'value': u'FastEthernet0/0'}) => {
    "item": {
        "key": "local_port",
        "value": "FastEthernet0/0"
    },
    "msg": "The key is local_port and the value is FastEthernet0/0"
}
ok: [R2] => (item={'key': u'management_ip', 'value': u'192.168.0.210'}) => {
    "item": {
        "key": "management_ip",
        "value": "192.168.0.210"
    },
    "msg": "The key is management_ip and the value is 192.168.0.210"
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0
{% endraw %}
```

## Git

This playbook is [**available here**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/Jinja/default/show_tidy.yml) in my Github repo.