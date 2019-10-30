---
title: Ansible set_facts
sub_heading: "Pass information between plays"
redirect_from: /ansible-set_facts/

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
**Reference: [Ansible](http://docs.ansible.com/ansible/set_fact_module.html)**

*   This module allows setting new variables. Variables are set on a host-by-host basis just like facts discovered by the setup module.
*   These variables will be available to subsequent plays during an `ansible-playbook` run, but will not be saved across executions even if you use a fact cache.
*   Per the standard Ansible variable precedence rules, many other types of variables have a higher priority, so this value may be overridden. See [http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) for more information.

```yaml
# Example setting host facts using key=value pairs, note that this always creates strings or booleans
- {% raw %}set_fact: one_fact="something" other_fact="{{ local_var }}"{% endraw %}

# Example setting host facts using complex arguments
- set_fact:
     one_fact: something
     {% raw %}other_fact: "{{ local_var * 2 }}"{% endraw %}
     {% raw %}another_fact: "{{ some_registered_var.results | map(attribute='ansible_facts.some_fact') | list }}"{% endraw %}

# As of 1.8, Ansible will convert boolean strings ('true', 'false', 'yes', 'no')
# to proper boolean values when using the key=value syntax, however it is still
# recommended that booleans be set using the complex argument style:
- set_fact:
    one_fact: true
    other_fact: false
```