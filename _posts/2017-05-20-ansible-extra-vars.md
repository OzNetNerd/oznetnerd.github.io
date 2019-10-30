---
title: Ansible --extra-vars
sub_heading: "What are they, anyway?"
redirect_from: /ansible-extra-vars/

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

Often you will see variables defined in a Playbook, a Task and/or a variables file such as group_vars or host_vars, as shown below:

_Variables in a Playbook:_
```yaml
- hosts: localhost
  connection: local
  gather_facts: no

  vars:
    provider:
      username: cisco
```

_Variables in a Task:_
```yaml
- hosts: localhost
  connection: local
  gather_facts: no

  - set_fact:
     hello: world
```

_Variables in a vars file:_
```yaml
provider:
  host: R1
  username: cisco
  password: cisco
  auth_pass: cisco
```

_A full list of variable locations and their order of precedence [**can be found here.**](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)_

What if you don't want to use statically configured variables? For example, maybe you need to run various show  commands and don't want to have to write a task for each. This is there --extra-vars  comes into great use.

Using this hosts file:

`hosts.ini`:

```
[gns3]
R1 ansible_host=192.168.0.210
R2 ansible_host=192.168.0.197
```

And this Playbook:

`show.yml:`

```yaml
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
  - debug: var=command

  - name: ntc_show
    ntc_show_command:
      provider: "{{ provider }}"
      connection: "{{ connection }}"
      command: "{{ command }}"
      secret: "{{ secret }}"
    register: results

  - name: Display output
    debug: var=results.response[0]
```

Gives us complete flexibility. Using it in conjunction with --extra-vars  allows us to specify:

*   The host or group we'd like to run the command on.
*   The ability to specify the credentials when running the command (as opposed to storing them in a variables file).
*   The platform (IOS, NXOS, etc).
*   Whether we'd like to use Telnet or SSH.
*   Which command we'd like to run.

It would be near impossible to replicate this functionality through the use of individual, static Tasks.

We can use this command to run the Playbook on the **'gns3'** group:

```bash
 ansible-playbook show.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=telnet platform=cisco_ios secret=cisco command='show version'"
```

Doing so produces the following output:

```yaml
PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1]
ok: [R2]

TASK [Display output] **********************************************************
ok: [R1] => {
    "results.response[0]": {
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
        "uptime": "1 hour, 54 minutes",
        "version": "12.4(15)T14"
    }
}
ok: [R2] => {
    "results.response[0]": {
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
        "uptime": "1 hour, 4 minutes",
        "version": "12.4(15)T14"
    }
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0
```

Excellent, it works! However, sending only one command isn't going to be all that useful. With a couple of minor changes to our code, we can now send multiple commands at once:

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

  - name: Display output
    debug: msg="{{ item.response[0] }}"
    with_items:
      - "{{ results.results }}"
{% endraw %}
```

To take advantage of our modifications, we simply need to send our commands in a list  as opposed to a string :

```bash
ansible-playbook show_untidy.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show ip interface brief']"
```

Let's take a look when we run this command:

```yaml
PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R2] => (item=show version)
ok: [R1] => (item=show ip interface brief)
ok: [R2] => (item=show ip interface brief)

TASK [Display output] **********************************************************
ok: [R1] => (item={'_ansible_parsed': True, u'changed': False, '_ansible_no_log': False, '_ansible_item_result': True, 'item': u'show version', 'invocation': {'module_name': u'ntc_show_command', u'module_args': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'delay': u'1', u'trigger_device_list': None, u'local_file': None, u'template_dir': u'ntc-templates/templates', u'use_keys': False, u'connection': u'ssh', u'use_templates': True, u'platform': u'********_ios', u'secret': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'command': u'show version', u'optional_args': {}, u'file': None, u'provider': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'platform': u'********_ios', u'host': u'192.168.0.210', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER'}, u'host': u'192.168.0.210', u'global_delay_factor': u'1', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'port': None, u'key_file': None, u'index_file': u'index'}}, u'response': [{u'running_image': u'//255.255.255.255/unknown', u'hostname': u'R1', u'uptime': u'2 hours, 11 minutes', u'config_register': u'0x2102', u'hardware': [u'3745'], u'version': u'12.4(15)T14', u'serial': [u'FTX0945W0MY'], u'rommon': u'ROMMON'}], u'response_list': []}) => {
    "item": {
        "changed": false,
        "invocation": {
            "module_args": {
                "command": "show version",
                "connection": "ssh",
                "delay": "1",
                "file": null,
                "global_delay_factor": "1",
                "host": "192.168.0.210",
                "index_file": "index",
                "key_file": null,
                "local_file": null,
                "optional_args": {},
                "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "platform": "********_ios",
                "port": null,
                "provider": {
                    "host": "192.168.0.210",
                    "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "platform": "********_ios",
                    "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "secret": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "template_dir": "ntc-templates/templates",
                "trigger_device_list": null,
                "use_keys": false,
                "use_templates": true,
                "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
            },
            "module_name": "ntc_show_command"
        },
        "item": "show version",
        "response": [
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
                "uptime": "2 hours, 11 minutes",
                "version": "12.4(15)T14"
            }
        ],
        "response_list": []
    },
    "msg": {
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
        "uptime": "2 hours, 11 minutes",
        "version": "12.4(15)T14"
    }
}
ok: [R1] => (item={'_ansible_parsed': True, u'changed': False, '_ansible_no_log': False, '_ansible_item_result': True, 'item': u'show ip interface brief', 'invocation': {'module_name': u'ntc_show_command', u'module_args': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'delay': u'1', u'trigger_device_list': None, u'local_file': None, u'template_dir': u'ntc-templates/templates', u'use_keys': False, u'connection': u'ssh', u'use_templates': True, u'platform': u'********_ios', u'secret': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'command': u'show ip interface brief', u'optional_args': {}, u'file': None, u'provider': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'platform': u'********_ios', u'host': u'192.168.0.210', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER'}, u'host': u'192.168.0.210', u'global_delay_factor': u'1', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'port': None, u'key_file': None, u'index_file': u'index'}}, u'response': [{u'status': u'up', u'intf': u'FastEthernet0/0', u'ipaddr': u'192.168.0.210', u'proto': u'up'}, {u'status': u'administratively down', u'intf': u'FastEthernet0/1', u'ipaddr': u'unassigned', u'proto': u'down'}], u'response_list': []}) => {
    "item": {
        "changed": false,
        "invocation": {
            "module_args": {
                "command": "show ip interface brief",
                "connection": "ssh",
                "delay": "1",
                "file": null,
                "global_delay_factor": "1",
                "host": "192.168.0.210",
                "index_file": "index",
                "key_file": null,
                "local_file": null,
                "optional_args": {},
                "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "platform": "********_ios",
                "port": null,
                "provider": {
                    "host": "192.168.0.210",
                    "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "platform": "********_ios",
                    "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "secret": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "template_dir": "ntc-templates/templates",
                "trigger_device_list": null,
                "use_keys": false,
                "use_templates": true,
                "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
            },
            "module_name": "ntc_show_command"
        },
        "item": "show ip interface brief",
        "response": [
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
        ],
        "response_list": []
    },
    "msg": {
        "intf": "FastEthernet0/0",
        "ipaddr": "192.168.0.210",
        "proto": "up",
        "status": "up"
    }
}
ok: [R2] => (item={'_ansible_parsed': True, u'changed': False, '_ansible_no_log': False, '_ansible_item_result': True, 'item': u'show version', 'invocation': {'module_name': u'ntc_show_command', u'module_args': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'delay': u'1', u'trigger_device_list': None, u'local_file': None, u'template_dir': u'ntc-templates/templates', u'use_keys': False, u'connection': u'ssh', u'use_templates': True, u'platform': u'********_ios', u'secret': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'command': u'show version', u'optional_args': {}, u'file': None, u'provider': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'platform': u'********_ios', u'host': u'192.168.0.197', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER'}, u'host': u'192.168.0.197', u'global_delay_factor': u'1', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'port': None, u'key_file': None, u'index_file': u'index'}}, u'response': [{u'running_image': u'//255.255.255.255/unknown', u'hostname': u'R2', u'uptime': u'2 hours, 11 minutes', u'config_register': u'0x2102', u'hardware': [u'3745'], u'version': u'12.4(15)T14', u'serial': [u'FTX0945W0MY'], u'rommon': u'ROMMON'}], u'response_list': []}) => {
    "item": {
        "changed": false,
        "invocation": {
            "module_args": {
                "command": "show version",
                "connection": "ssh",
                "delay": "1",
                "file": null,
                "global_delay_factor": "1",
                "host": "192.168.0.197",
                "index_file": "index",
                "key_file": null,
                "local_file": null,
                "optional_args": {},
                "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "platform": "********_ios",
                "port": null,
                "provider": {
                    "host": "192.168.0.197",
                    "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "platform": "********_ios",
                    "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "secret": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "template_dir": "ntc-templates/templates",
                "trigger_device_list": null,
                "use_keys": false,
                "use_templates": true,
                "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
            },
            "module_name": "ntc_show_command"
        },
        "item": "show version",
        "response": [
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
                "uptime": "2 hours, 11 minutes",
                "version": "12.4(15)T14"
            }
        ],
        "response_list": []
    },
    "msg": {
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
        "uptime": "2 hours, 11 minutes",
        "version": "12.4(15)T14"
    }
}
ok: [R2] => (item={'_ansible_parsed': True, u'changed': False, '_ansible_no_log': False, '_ansible_item_result': True, 'item': u'show ip interface brief', 'invocation': {'module_name': u'ntc_show_command', u'module_args': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'delay': u'1', u'trigger_device_list': None, u'local_file': None, u'template_dir': u'ntc-templates/templates', u'use_keys': False, u'connection': u'ssh', u'use_templates': True, u'platform': u'********_ios', u'secret': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'command': u'show ip interface brief', u'optional_args': {}, u'file': None, u'provider': {u'username': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'platform': u'********_ios', u'host': u'192.168.0.197', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER'}, u'host': u'192.168.0.197', u'global_delay_factor': u'1', u'password': u'VALUE_SPECIFIED_IN_NO_LOG_PARAMETER', u'port': None, u'key_file': None, u'index_file': u'index'}}, u'response': [{u'status': u'up', u'intf': u'FastEthernet0/0', u'ipaddr': u'192.168.0.197', u'proto': u'up'}, {u'status': u'administratively down', u'intf': u'FastEthernet0/1', u'ipaddr': u'unassigned', u'proto': u'down'}], u'response_list': []}) => {
    "item": {
        "changed": false,
        "invocation": {
            "module_args": {
                "command": "show ip interface brief",
                "connection": "ssh",
                "delay": "1",
                "file": null,
                "global_delay_factor": "1",
                "host": "192.168.0.197",
                "index_file": "index",
                "key_file": null,
                "local_file": null,
                "optional_args": {},
                "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "platform": "********_ios",
                "port": null,
                "provider": {
                    "host": "192.168.0.197",
                    "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "platform": "********_ios",
                    "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "secret": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                "template_dir": "ntc-templates/templates",
                "trigger_device_list": null,
                "use_keys": false,
                "use_templates": true,
                "username": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
            },
            "module_name": "ntc_show_command"
        },
        "item": "show ip interface brief",
        "response": [
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
        ],
        "response_list": []
    },
    "msg": {
        "intf": "FastEthernet0/0",
        "ipaddr": "192.168.0.197",
        "proto": "up",
        "status": "up"
    }
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0
```

WOW, look at the size of that output! Is that a mistake? No, it's not. This is done intentionally and is a result of the loop being added to the Playbook. (See the **[Cleaning Up Ansible Loop Outputs](/cleaning-ansible-loop-outputs/)** post for information on how to clean this up.)

If we sift through the output we can see that our Playbook did in fact work as desired, as per the sections below:

`show_unity.yml`:

```yaml
    "msg": {
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
        "uptime": "2 hours, 11 minutes",
        "version": "12.4(15)T14"
    }
    "msg": {
        "intf": "FastEthernet0/0",
        "ipaddr": "192.168.0.210",
        "proto": "up",
        "status": "up"
    }



    "msg": {
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
        "uptime": "2 hours, 11 minutes",
        "version": "12.4(15)T14"
    }
    "msg": {
        "intf": "FastEthernet0/0",
        "ipaddr": "192.168.0.197",
        "proto": "up",
        "status": "up"
    }
```

## Git

This playbook is [**available here**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/extra-vars/show_untidy.yml) in my Github repo.