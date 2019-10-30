---
title: Ansible vars Lists
sub_heading: "Using lists as variables"
redirect_from: /ansible-vars-lists/

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
Lists can be provided to a module through vars specified inside a Playbook or they can be provided directly inside a [**Task**](/tags/#jinja2/ansible/tasks/).

In the examples below we want to send two **"show"** commands to the devices. The former example uses a vars  list while the latter defines the list inside of the module call:

List inside vars:

```yaml
- name: run show commands
  hosts: all
  connection: local
  gather_facts: no

  vars:
    command_list:
      - show interface summary
      - show ip interface brief

  tasks:
    - name: run show commands
      ios_command:
        provider: "{{ provider }}"
        {% raw %}commands: "{{ command_list }}"{% endraw %}
      register: show_output
```

List inside Task:

```yaml
- name: show commands
  ios_command:
    provider: "{{ provider }}"
    commands:
      - show interface summary
      - show ip interface brief
  register: show_output
```

Both methods work perfectly fine, though the former is more flexible as it can be reused across multiple Tasks.