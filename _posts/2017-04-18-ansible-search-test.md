---
title: Ansible search() Test
sub_heading: "Let's take search() for a ride"
redirect_from: /ansible-search-test/

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
**Reference: [Ansible](https://docs.ansible.com/ansible/playbooks_tests.html)**

match  requires a complete match in the string, while search  only requires matching a subset of the string.

## Example

As per the [selectattr( )  **page**](/jinja2-selectattr-filter/), the Jinja [equalto( )](http://jinja.pocoo.org/docs/2.9/templates/#equalto)  Test, as well as the Ansible [match( )](https://docs.ansible.com/ansible/playbooks_tests.html#testing-strings)  and [search( )](https://docs.ansible.com/ansible/playbooks_tests.html#testing-strings)  Tests all work in a similar fashion.

Using this dictionary:

```yaml
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    network:
      addresses:
        private_ext:
          - type: fixed
            addr: 172.16.2.100
        private_man:
          - type: fixed
            addr: 172.16.1.100
          - type: floating
            addr: 10.90.80.10
```

We can obtain the **"floating"** value using any one of following filters:

```yaml
- debug: msg={% raw %}{{ network.addresses.private_man | selectattr("type", "equalto", "floating") | map(attribute='addr') | list }}{% endraw %}
- debug: msg={% raw %}{{ network.addresses.private_man | selectattr("type", "match", "^floating$") | map(attribute='addr') | list }}{% endraw %}
- debug: msg={% raw %}{{ network.addresses.private_man | selectattr("type", "search", "^floating$") | map(attribute='addr') | list }}{% endraw %}
```

The above lines result in the following output:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}```