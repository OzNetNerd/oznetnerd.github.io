---
title: Ansible Minimum Viable Playbook (MVP)
sub_heading: "Start with the basics"
redirect_from: /ansible-minimum-viable-playbook-mvp/

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
**Reference: [Adam’s Tech Blog](https://adamj.eu/tech/2015/04/18/ansible-minimum-viable-playbook/)**

The Minimum Viable Playbook (MVP) is the shortest, most useful Ansible playbook I have. Whenever I need to write some Ansible code and I’m not entirely sure I’m doing it right (which is often), I implement it first in the MVP so I can test it quickly. Here’s my latest iteration:

```yaml
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    my_name: a_b_c
  tasks:
    - debug: msg={% raw %}{{ my_name|replace('_', '-') }}{% endraw %}
```

I keep the MVP saved as ~/tmp/test.yml , from which it can be run with:

```bash
$ ansible-playbook -i localhost, ~/tmp/test.yml
```

Two things make the MVP particularly fast:

1.  When running tasks locally, Ansible always uses the _local_ connection type, which means there is no SSH overhead.
2.  I’ve disabled fact-gathering which means my tasks are executed immediately. This saves about a second per run, which isn’t much but you do notice it.