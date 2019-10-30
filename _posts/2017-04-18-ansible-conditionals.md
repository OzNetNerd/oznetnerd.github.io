---
title: Ansible Conditionals
sub_heading: "Add flexibility to your configs with Conditionals"
redirect_from: /ansible-conditionals/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Ansible
- Automation
- DevOps
- Jinja2
---
## Inline expressions

**Reference: [Inline 'if' expressions](http://stackoverflow.com/questions/14214942/python-jinja2-shorthand-conditional)**

It's possible to use **[inline if-expressions](http://jinja.pocoo.org/docs/templates/#if-expression)**:

```
{% raw %}{{ 'Update' if files else 'Continue' }} {% endraw %}
```

**Note:** See the [**Jinja2 Filters**](/tags/#jinja2) section for more information.

## 'if/else' example

**Reference: [cidr block's git repo](https://github.com/cidrblock/sot_vlans/blob/master/roles/determine_os/tasks/main.yml)**

```
{% raw %}"{{ 'unknown' if ssh_output|failed else ssh_output['stdout'][0]|determine_os}}"{% endraw %}
```

**Note:** See the [**Jinja2 Filters**](/tags/#jinja2) section for more information.

## 'When' example

**Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_conditionals.html)**

```yaml
tasks:
    - shell: echo "I've got '{{ foo }}' and am not afraid to use it!"
      when: foo is defined

    - fail: msg="Bailing out. this play requires 'bar'"
      when: bar is undefined
```