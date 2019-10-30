---
title: Understanding Ansible Output Structure
sub_heading: "There IS a method behind the madness"
redirect_from: /understanding-ansible-output-structure/

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

In the [**Ansible --extra-vars post**](/ansible-extra-vars/) we saw just how out of control outputs can be. The [**Cleaning Up Ansible Loop Outputs**](/cleaning-ansible-loop-outputs/) post then demonstrated how we can remove all of the unnecessary information.

This post is aimed at answering the question, _"How do we navigate through the output in order to extract the information we need?"_. For example, how would we extract the `management_ip` information from the output below (which was generated as a result of running [**this Playbook**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/extra-vars/show_untidy.yml)):

_Ansible `--extra-vars` output:_

```
$ ansible-playbook extract_ip.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show cdp neighbor detail']"

PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R2] => (item=show version)
ok: [R2] => (item=show cdp neighbor detail)
ok: [R1] => (item=show cdp neighbor detail)
ut contains the output of 
TASK [Display output] **********************************************************
ok: [R1] => {
    "msg": {
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
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
                        "uptime": "47 minutes",
                        "version": "12.4(15)T14"
                    }
                ],
                "response_list": []
            },
            {
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
                "changed": false,
                "invocation": {
                    "module_args": {
                        "command": "show cdp neighbor detail",
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
                "item": "show cdp neighbor detail",
                "response": [
                    {
                        "destination_host": "R2.lab",
                        "local_port": "FastEthernet0/0",
                        "management_ip": "192.168.0.197",
                        "platform": "Cisco 3745",
                        "remote_port": "FastEthernet0/0",
                        "software_version": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
                    }
                ],
                "response_list": []
            }
        ]
    }
}
ok: [R2] => {
    "msg": {
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
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
                        "uptime": "47 minutes",
                        "version": "12.4(15)T14"
                    }
                ],
                "response_list": []
            },
            {
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
                "changed": false,
                "invocation": {
                    "module_args": {
                        "command": "show cdp neighbor detail",
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
                "item": "show cdp neighbor detail",
                "response": [
                    {
                        "destination_host": "R1.lab.local",
                        "local_port": "FastEthernet0/0",
                        "management_ip": "192.168.0.210",
                        "platform": "Cisco 3745",
                        "remote_port": "FastEthernet0/0",
                        "software_version": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
                    }
                ],
                "response_list": []
            }
        ]
    }
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0
```

The answer is simple, if we know how to make our way through lists & dictionaries.

_Note: This output contains the output of both the_ `show version`_and_ `show cdp neighbor detail`_ commands. In this post we're only interested in extracting information from the latter command. The former command was included to show how to navigate outputs which contain multiple dictionaries. _

## Step by Step Guide

### Navigation Tips

However, before we get started let's first look at some techniques which will help us achieve our goal.

1.  Copy the output into an advanced text editor like Notepad++.
2.  Next, change the language to Python. This will give us syntax highlighting which is nice, but what we're really after is the ability to collapse blocks of code. Doing this enables us clearly see the output's hierarchy.
3.  Click the `-`  to the left of  the `ok: [R1] => {`  text collapses all of **R1's**  output. The same is true for **R2's** output as we can see on lines `12`  and `122`  respectively as per the image below. This tells us that they're two completely independent outputs. Further to this, because their outputs were generated using exactly the same method, we're guaranteed that whatever information we extract from one of them, we will be able to use exact the same method for the other too.  It is for this reason that the rest of this guide will only refer to **R1** from here on out.

[![](/assets/2017/05/Collapsed_Routers.png)](/assets/2017/05/Collapsed_Routers.png)

4.  Click the `+`  to the left of **R1** to expand its output again.
5.  Clicking on the inner side of a curly brace and square bracket show us which output resides inside of that dictionary or list respectively. For example, in the image below we can see all of the dictionaries and lists which reside inside of the `module_args`  dictionary:

[![](/assets/2017/05/Dict_Block.png)](/assets/2017/05/Dict_Block.png)

Now let's see how to put these two techniques into good use.

### Navigating

The first block we see is `msg` . It is there as a result of us using the [**debug module's**](http://docs.ansible.com/ansible/debug_module.html)  `msg`  parameter [**in the Playbook**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/set_facts/extract_ip.yml#L37). Next we see a couple of dictionary entries:

```json
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
```

We can tell that `changed`  and `msg`  won't help us get to `management_ip`  because they're stubs - they do not contain nested dictionaries or lists as is evident by their lack of curly brace and square brackets respectively which therefore means they have no `-`  sign to the left of them either.

However, we can see that `results`  list has the trifecta. A curly brace, a square bracket and a `-`  too! Further to this, by clicking on the right of the square bracket (`[` ), we can see that this list encompasses the rest R1's output. On the other hand, if we click on the right side of the the curly brace (`{` ) we see this dictionary covers the first command's output. On line 70 we see the dictionary close, then a comma, then a second dictionary open begins, like so:

```json
            },
            {
```

As this second dictionary contains the `show cdp neighbor detail`  output, we'll focus our attention on it.

In this dictionary we see a a similar story to what we saw earlier. We've got a list (`response`) which has an embedded dictionary. Further to this, the embedded dictionary contains the key: value pair we're looking for - `"management_ip": "192.168.0.197"` :

```json
                "response": [
                    {
                        "destination_host": "R2.lab",
                        "local_port": "FastEthernet0/0",
                        "management_ip": "192.168.0.197",
                        "platform": "Cisco 3745",
                        "remote_port": "FastEthernet0/0",
                        "software_version": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
                    }
                ]
```

Before proceeding, let's summarise what we've worked out so far. A screenshot of my text editor has been included below to assist you with following along:

1.  The `results`  list is at the root of the tree and contains the information we need, though that information is nested.
2.  Inside of results  are two dictionaries, one which contains the `show version`  output, and the other which contains the `show cdp neighbor detail`  output. _(The first dictionary ends on line 70 and the second dictionary starts on line 71.)_
3.  As we're only looking to extract the `management_ip`  information from the `show cdp neighbor detail`  output, we focused our attention on the second dictionary.
4.  Inside the second dictionary resides a list called `response` . _(Response starts on line 107.)_
5.  Inside the response list is a dictionary which contains a number of key: value pairs. One of which is `"management_ip": "192.168.0.197"` . _(This dictionary starts on line 108.)_

[![](/assets/2017/05/Response.png)](/assets/2017/05/Response.png)

### Going Live!

Now that we know the theory, let's put it into practice. To get the original, untouched output, we use this Playbook:

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
    debug: msg="{{ output }}"
```

This produces the output we saw at the top of this post so I won't waste your time by pasting another copy of it here. All we're doing in this Playbook at this point in time is registering the command outputs to a variable called `output` and then printing the contents of that variable.

In other words, the outputs we've been looking at throughout this post are stored in the `output` variable and we'll therefore need to build on it/dig into it in order to extract the information we need. Speaking of which, let's go ahead and do that now.

As we discovered earlier, the root of the tree we need to go down is the `results`  list. Further to this, do you remember how we saw this on lines 70 and 71:

```json
            },
            {
```

At the time I mentioned that this was the ending of one dictionary and the beginning of another. What I didn't mentioned (because I wanted to avoid information overload!) was that the comma represents the separation of two indexes in the `results`  list.

Therefore the first dictionary we spoke of (which contains the `show version`  output resides in `results[0]`  while the second dictionary (which contains the `show cdp neighbor detail`  output) resides in `results[1]` .

Let's put this information to the test and edit the last line of our Playbook, like so:

```yaml
{% raw %}
  - name: Display output
    debug: msg="{{ output.results[1] }}"
{% endraw %}
```

Running the Playbook again gives us this:

```
{% raw %}
$ ansible-playbook extract_ip.yml -i hosts.ini --extra-vars "hosts=R1 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show cdp neighbor detail']"

PLAY [R1] **********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R1] => (item=show cdp neighbor detail)

TASK [Display output] **********************************************************
ok: [R1] => {
    "msg": {
        "changed": false,
        "invocation": {
            "module_args": {
                "command": "show cdp neighbor detail",
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
        "item": "show cdp neighbor detail",
        "response": [
            {
                "destination_host": "R2.lab",
                "local_port": "FastEthernet0/0",
                "management_ip": "192.168.0.197",
                "platform": "Cisco 3745",
                "remote_port": "FastEthernet0/0",
                "software_version": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
            }
        ],
        "response_list": []
    }
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
{% endraw %}
```

Great, we've now successfully isolated the `show cdp neighbor detail`  information from the `show version`  information.

Next up is `response` . Because it is a list, we'll need to specify the index we're after. Given that there is only one item in this list, we'll need to use index `0` . Making this change results in the last two lines of our Playbook looking like this:

```
{% raw %}
  - name: Display output
    debug: msg="{{ output.results[1].response[0] }}"
{% endraw %}    
```

And the output generated by the Playbook looking like this:

```
{% raw %}
$ ansible-playbook extract_ip.yml -i hosts.ini --extra-vars "hosts=R1 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show cdp neighbor detail']"

PLAY [R1] **********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R1] => (item=show cdp neighbor detail)

TASK [Display output] **********************************************************
ok: [R1] => {
    "msg": {
        "destination_host": "R2.lab",
        "local_port": "FastEthernet0/0",
        "management_ip": "192.168.0.197",
        "platform": "Cisco 3745",
        "remote_port": "FastEthernet0/0",
        "software_version": "Cisco IOS Software, 3700 Software (C3745-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)"
    }
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
{% endraw %}
```

And now all we need to do is access extract the management IP address. Given that we're dealing with a dictionary as opposed to a list, we'll need to specify the key as opposed to an index. This results in the last two lines of our Playbook looking like this:

```
{% raw %}
  - name: Display output
    debug: msg="{{ output.results[1].response[0].management_ip }}"
{% endraw %}
```

And the output of our Playbook looking like this:

```
{% raw %}
$ ansible-playbook extract_ip.yml -i hosts.ini --extra-vars "hosts=R1 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command=['show version','show cdp neighbor detail']"

PLAY [R1] **********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R1] => (item=show version)
ok: [R1] => (item=show cdp neighbor detail)

TASK [Display output] **********************************************************
ok: [R1] => {
    "msg": "192.168.0.197"
}

PLAY RECAP *********************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
{% endraw %}
```

And there you have it, we've managed to extract the management IP address! Note however that this only prints the management IP. If we want to assign it to a variable for later use, we can use [**set_facts**](/ansible-set_facts/) to do so:

```
{% raw %}
  - name: set fact
    set_fact:
      mgmt_ip: "{{ output.results[1].response[0].management_ip }}"

  - name: Display output
    debug: msg="{{ mgmt_ip }}"
{% endraw %}
```

## Git

This playbook is [**available here**](https://github.com/OzNetNerd/Ansible_Playbooks/blob/master/set_facts/extract_ip.yml) in my Github repo.