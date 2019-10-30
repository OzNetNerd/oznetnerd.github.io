---
title: Ansible Built-in Variables
sub_heading: "Using built in variables effectively"
redirect_from: /ansible-built-variables/

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
Ansible has built in variables which you can come in handy with some Playbooks. They can be viewed using the following [**Ad-Hoc**](/ansible-ad-hoc-commands/) command:

```bash
ansible all -i 192.168.0.211, -m debug -a "msg={{ vars }}"
```

or this Task:

```yaml
{% raw %}---{% endraw %}
- name: Display vars
  debug: msg={{ vars }}
```

_**Note that the latter provides much more information than the former.**_

When the above Task is run, one of the pieces of information it provides is this:

```yaml
"inventory_hostname_short": "R2",
```

We can then use this information as a variable in our other Tasks, like so:

```yaml
{% raw %}---{% endraw %}
- name: ios_comamnd - Run the 'show interfaces' command
  ios_command:
    provider: "{{ provider }}"
    authorize: yes
    commands:
      - show run
  register: show_out

-  local_action: copy content={% raw %}"{{ show_out.stdout_lines[0]|join('\n') }}"{% endraw %} dest="/tmp/{% raw %}{{inventory_hostname_short}}{% endraw %}_show_run.txt"
```

The result is that each device will automatically output their show run  into a file which matches their hostname, as per the output below:

```bash
$ ls -l /tmp/R[12]*
-rw-rw-r-- 1 will will 1654 Apr 16 20:58 /tmp/R1_output_fact.txt
-rw-rw-r-- 1 will will 1654 Apr 16 20:58 /tmp/R2_output_fact.txt
```