---
title: Jinja2 map() Filter
sub_heading: "Let's take map() for a test drive"
redirect_from: /jinja2-map-filter/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- Jinja2

tags:
- Ansible
- Automation
- Jinja2
- DevOps
---
**Reference: [jinja](http://jinja.pocoo.org/docs/dev/templates/#map)**

Applies a filter on a sequence of objects or looks up an attribute. This is useful when dealing with lists of objects but you are really only interested in a certain value of it.

The basic usage is mapping on an attribute. Imagine you have a list of users but you are only interested in a list of usernames:

Users on this page:

```
{% raw %}{{ users|map(attribute='username')|join(', ') }}{% endraw %}
```

Alternatively you can let it invoke a filter by passing the name of the filter and the arguments afterwards. A good example would be applying a text conversion filter on a sequence:

```
Users on this page: {% raw %}{{ titles|map('lower')|join(', ') }}{% endraw %}
```

## Examples

**Reference:** [**OzNetNerd.com**](oznetnerd.com)

The code below passes the `names` dictionary to the `map` filter on four separate occasions to demonstrate the ways in which it can be used:

``` {% raw %}- hosts: localhost connection: local gather_facts: no vars: names: - first: Paul last: Thompson - first: Rod last: Johnson tasks: - debug: msg={{ names | map(attribute='first') | list }} - debug: msg={{ names | map(attribute='last') | list }} - debug: msg={{ names | map('upper') | list }} - debug: msg={{ names | map(attribute='last') | map('upper') | list }}{% endraw %}```

### Output #1

In this run we see that the `map(attribute='first')` filter prints the **"first"** values of the Key Value pairs:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "Paul", 
        "Rod"
    ]
}
```

### Output #2

Similar to the previous run, we see that the `map(attribute='last')` filter prints the **"last"** values of the Key Value pairs:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "Thompson", 
        "Johnson"
    ]
}
```

### Output #3

This example differs from the previous two in that we're passing the `upper` Jinja filter to `map` as opposed to an attribute:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "{U'LAST': U'THOMPSON', U'FIRST': U'PAUL'}", 
        "{U'LAST': U'JOHNSON', U'FIRST': U'ROD'}"
    ]
}
```

### Output #4

This example demonstrates how we can chain two `map` filters. Note that one is being passed an attribute while the other is being passed the `upper` filter:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "THOMPSON", 
        "JOHNSON"
    ]
}
```

## Cisco Example

The following Playbook uses `map()` to extract interface information from Cisco devices:

```yaml
- hosts: "{{ hosts }}"
  connection: local
  gather_facts: no

  vars:
    provider:
     {% raw %} host: "{{ ansible_host }}"{% endraw %}
      username: "{{ username }}"
      password: "{{ password }}"
      platform: "{{ platform }}"

  tasks:

  - name: ntc_show
    ntc_show_command:
      provider: "{{ provider }}"
      connection: "{{ connection }}"
      command: "{{ command }}"
      secret: "{{ secret }}"
    register: results

  - name: DEBUG - print original results
    debug: msg={{ results }}

  - name: DEBUG - print extracted results
    {% raw %}debug: msg={{ results.response | map(attribute='intf') | list }}{% endraw %}
```

Running this Playbook results in the following output: ```

```yaml
$ ansible-playbook interface_map.yml -i hosts.ini --extra-vars "hosts=gns3 username=cisco password=cisco connection=ssh platform=cisco_ios secret=cisco command='show ip interface brief'"

PLAY [gns3] ********************************************************************

TASK [ntc_show] ****************************************************************
ok: [R2]
ok: [R1]

TASK [DEBUG - print original results] ******************************************
ok: [R1] => {
    "msg": {
        "changed": false,
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
    }
}
ok: [R2] => {
    "msg": {
        "changed": false,
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
    }
}

TASK [DEBUG - print extracted results] *****************************************
ok: [R1] => {
    "msg": [
        "FastEthernet0/0",
        "FastEthernet0/1"
    ]
}
ok: [R2] => {
    "msg": [
        "FastEthernet0/0",
        "FastEthernet0/1"
    ]
}

PLAY RECAP *********************************************************************
R1                         : ok=3    changed=0    unreachable=0    failed=0
R2                         : ok=3    changed=0    unreachable=0    failed=0
```

## Source

These Playbooks are available in the **[map() section](https://github.com/OzNetNerd/Ansible_Playbooks/tree/master/Jinja/map)** of my Github repo.